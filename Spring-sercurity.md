# Spring Security JWT 인증/인가 구현 정리

## 1. 기술 스택

| 항목 | 내용 |
|------|------|
| Framework | Spring Boot 3.2.0 |
| Security | Spring Security 6.2.0 |
| 인증 방식 | JWT (JSON Web Token) |
| DB | MySQL 8.x |
| ORM | Spring Data JPA + Hibernate |
| Build | Gradle 8.5 |
| Java | Java 17 |

---

## 2. 프로젝트 구조

```
src/main/java/com/example/security/
├── SecurityApplication.java              ← Spring Boot 진입점
│
├── global/
│   ├── config/
│   │   └── SecurityConfig.java           ← Security 핵심 설정
│   ├── jwt/
│   │   ├── JwtTokenProvider.java         ← JWT 생성 / 검증
│   │   ├── JwtAuthenticationFilter.java  ← 요청마다 JWT 검사 필터
│   │   ├── JwtAuthenticationEntryPoint   ← 401 응답 처리
│   │   └── JwtAccessDeniedHandler.java   ← 403 응답 처리
│   └── exception/
│       └── GlobalExceptionHandler.java   ← 전역 예외 처리
│
└── domain/
    ├── auth/
    │   ├── controller/AuthController.java ← 인증 API
    │   └── service/AuthService.java       ← 인증 비즈니스 로직
    └── user/
        ├── entity/
        │   ├── User.java                  ← 유저 엔티티 (UserDetails 구현)
        │   ├── Role.java                  ← USER / ADMIN enum
        │   └── RefreshToken.java          ← Refresh Token 엔티티
        ├── repository/
        │   ├── UserRepository.java
        │   └── RefreshTokenRepository.java
        └── service/
            └── CustomUserDetailsService.java
```

---

## 3. 인증 흐름

### 3-1. 회원가입
```
클라이언트 → POST /api/auth/signup
         → 비밀번호 BCrypt 암호화
         → users 테이블에 저장 (기본 Role: USER)
         → 유저 정보 반환
```

### 3-2. 로그인
```
클라이언트 → POST /api/auth/login { email, password }
         → AuthenticationManager로 이메일/비밀번호 검증
         → Access Token (30분) + Refresh Token (7일) 발급
         → Refresh Token은 DB(refresh_tokens 테이블)에 저장
         → 두 토큰을 클라이언트에 반환
```

### 3-3. 인증이 필요한 API 호출
```
클라이언트 → 요청 헤더에 토큰 첨부
            Authorization: Bearer {accessToken}
         → JwtAuthenticationFilter에서 토큰 추출 및 검증
         → 유효하면 SecurityContext에 인증 정보 저장
         → 컨트롤러 로직 실행
```

### 3-4. Access Token 재발급
```
클라이언트 → POST /api/auth/reissue { refreshToken }
         → Refresh Token 유효성 검증
         → DB에 저장된 Refresh Token과 비교
         → 새 Access Token + 새 Refresh Token 발급 (Rotation 방식)
         → DB의 Refresh Token 갱신
```

### 3-5. 로그아웃
```
클라이언트 → POST /api/auth/logout (Access Token 필요)
         → DB에서 해당 유저의 Refresh Token 삭제
         → 이후 재발급 불가 상태로 전환
```

---

## 4. JWT 구현 방식

### 토큰 종류

| 종류 | 만료 시간 | 용도 |
|------|-----------|------|
| Access Token | 30분 | API 인증에 사용 |
| Refresh Token | 7일 | Access Token 재발급용 |

### 토큰 구조 (Payload)
```
Access Token  : { sub: email, auth: "ROLE_USER", iat, exp }
Refresh Token : { sub: email, iat, exp }
```

### JwtTokenProvider 주요 기능
- `generateAccessToken()` : 인증 객체로 Access Token 생성
- `generateRefreshToken()` : 이메일로 Refresh Token 생성
- `getAuthentication()` : 토큰에서 Authentication 객체 추출
- `validateToken()` : 토큰 유효성 검증 (만료/변조/형식 오류 처리)

### Refresh Token Rotation
재발급 시 Refresh Token도 함께 새로 발급하여 DB를 갱신하는 방식.  
탈취된 Refresh Token을 한 번만 사용 가능하게 만들어 보안성을 높임.

---

## 5. Spring Security 설정

### SecurityConfig 핵심 설정
```java
// CSRF 비활성화 (JWT는 Stateless라 불필요)
.csrf(AbstractHttpConfigurer::disable)

// 세션 비활성화 (JWT 방식이므로 서버에 세션 미저장)
.sessionManagement(session ->
    session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))

// URL별 접근 권한 설정
.authorizeHttpRequests(auth -> auth
    .requestMatchers("/api/auth/**").permitAll()   // 인증 불필요
    .requestMatchers("/api/admin/**").hasRole("ADMIN")  // ADMIN만
    .anyRequest().authenticated())                 // 나머지는 로그인 필요

// JWT 필터 등록 (Security 필터 체인 앞에 추가)
.addFilterBefore(new JwtAuthenticationFilter(jwtTokenProvider),
    UsernamePasswordAuthenticationFilter.class)
```

### 예외 처리
| 상황 | 처리 클래스 | 응답 |
|------|-------------|------|
| 토큰 없이 인증 필요 API 접근 | JwtAuthenticationEntryPoint | 401 Unauthorized |
| 권한 없는 API 접근 | JwtAccessDeniedHandler | 403 Forbidden |

---

## 6. 인가 방식

### 방법 1 - URL 기반 (SecurityConfig)
```java
.requestMatchers("/api/admin/**").hasRole("ADMIN")
.requestMatchers("/api/user/**").hasAnyRole("USER", "ADMIN")
```

### 방법 2 - 메서드 기반 (@PreAuthorize)
```java
@PreAuthorize("hasRole('ADMIN')")
public ResponseEntity<?> adminOnly() { ... }

@PreAuthorize("hasRole('USER') or hasRole('ADMIN')")
public ResponseEntity<?> userOrAdmin() { ... }
```

`@EnableMethodSecurity`를 SecurityConfig에 선언해야 `@PreAuthorize` 사용 가능.

---

## 7. DB 테이블 구조

### users
| 컬럼 | 타입 | 설명 |
|------|------|------|
| id | BIGINT (PK) | 자동 증가 |
| email | VARCHAR (Unique) | 로그인 ID |
| password | VARCHAR | BCrypt 암호화 저장 |
| name | VARCHAR | 이름 |
| role | ENUM | USER / ADMIN |

### refresh_tokens
| 컬럼 | 타입 | 설명 |
|------|------|------|
| id | BIGINT (PK) | 자동 증가 |
| email | VARCHAR (Unique) | 유저 이메일 |
| token | VARCHAR(500) | Refresh Token 값 |

---

## 8. API 명세

| Method | URL | 인증 | 권한 | 설명 |
|--------|-----|------|------|------|
| POST | `/api/auth/signup` | 없음 | - | 회원가입 |
| POST | `/api/auth/login` | 없음 | - | 로그인 (토큰 발급) |
| POST | `/api/auth/reissue` | 없음 | - | Access Token 재발급 |
| POST | `/api/auth/logout` | 필요 | USER | 로그아웃 |
| GET | `/api/public/hello` | 없음 | - | 공개 API |
| GET | `/api/user/me` | 필요 | USER | 내 정보 조회 |
| GET | `/api/user/dashboard` | 필요 | USER | 유저 대시보드 |
| GET | `/api/admin/dashboard` | 필요 | ADMIN | 관리자 전용 |

---

## 9. 보안 고려사항

- **비밀번호**: BCryptPasswordEncoder로 단방향 암호화 저장
- **JWT Secret**: 256bit 이상의 랜덤 키 사용 (Base64 인코딩)
- **Refresh Token**: DB 저장 및 Rotation 방식으로 탈취 대응
- **Stateless**: 서버에 세션 미저장, 확장성 확보
- **역할 분리**: USER / ADMIN으로 접근 권한 분리
