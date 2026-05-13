# Spring Security로 인증과 인가 구현

## Spring Security란?

Spring 기반 애플리케이션의 인증(Authentication)과 인가(Authorization)를 담당하는 보안 프레임워크이다.
로그인, 권한 체크, 비밀번호 암호화 등 보안 관련 기능을 제공한다.

---

## 인증 (Authentication)

사용자가 누구인지 확인하는 과정이다.

이번 프로젝트에서는 **JWT 토큰 기반 인증**을 사용했다.

### 회원가입

사용자가 이메일, 비밀번호, 이름을 입력하면 DB에 저장한다.
이때 비밀번호는 그대로 저장하지 않고 암호화해서 저장한다.

```java
passwordEncoder.encode(password)
```

BCrypt 알고리즘으로 암호화하기 때문에 DB가 털려도 원본 비밀번호를 알 수 없다.

### 로그인

사용자가 이메일과 비밀번호를 입력하면 Spring Security가 검증한다.

```java
// DB에서 사용자 정보를 가져와 Spring Security에 전달
loadUserByUsername(email)
```

이메일로 DB에서 유저를 찾고, 입력한 비밀번호와 암호화된 비밀번호를 비교한다.
일치하면 로그인 성공이고, **Access Token과 Refresh Token을 발급**한다.

```
로그인 성공 응답 예시

{
    "accessToken": "eyJhbGci...",
    "refreshToken": "eyJhbGci...",
    "tokenType": "Bearer"
}
```

| 토큰 | 만료 시간 | 용도 |
|------|-----------|------|
| Access Token | 30분 | API 요청 시 인증에 사용 |
| Refresh Token | 7일 | Access Token 만료 시 재발급용 |

### 인증 필터

로그인 이후 API를 호출할 때마다 토큰을 헤더에 담아서 보낸다.

```
Authorization: Bearer eyJhbGci...
```

서버에서는 **JwtAuthenticationFilter**가 모든 요청을 가로채서 토큰을 검사한다.

```java
// 요청 헤더에서 토큰 추출 후 검증
// 유효하면 SecurityContext에 사용자 정보 저장
// 이후 컨트롤러에서 인증된 사용자로 처리
```

토큰이 없거나 만료되면 **401 Unauthorized**를 반환한다.

---

## 인가 (Authorization)

인증된 사용자가 어떤 권한을 가지고 있는지 확인하는 과정이다.

이번 프로젝트에서는 **USER**와 **ADMIN** 두 가지 권한을 사용했다.

회원가입 시 기본으로 `ROLE_USER` 권한을 부여한다.

```java
User.builder()
    .email(email)
    .password(encodedPassword)
    .name(name)
    .role(Role.USER)   // 기본 권한 USER 부여
    .build();
```

### URL 기반 인가

SecurityConfig에서 URL별로 접근 권한을 설정했다.

```java
.authorizeHttpRequests(auth -> auth
    .requestMatchers("/api/auth/**").permitAll()         // 누구나 접근 가능
    .requestMatchers("/api/admin/**").hasRole("ADMIN")   // ADMIN만 접근 가능
    .anyRequest().authenticated()                        // 나머지는 로그인 필요
)
```

`/api/admin/**` 경로는 ROLE_ADMIN이 아니면 **403 Forbidden**을 반환한다.

### 메서드 기반 인가

컨트롤러 메서드에 `@PreAuthorize`를 붙여서 권한을 체크할 수도 있다.

```java
// ADMIN만 접근 가능
@PreAuthorize("hasRole('ADMIN')")
public ResponseEntity<?> adminDashboard() { ... }

// USER 또는 ADMIN 접근 가능
@PreAuthorize("hasRole('USER') or hasRole('ADMIN')")
public ResponseEntity<?> userDashboard() { ... }
```

---

## 동작 흐름

```
1. 사용자가 API 요청을 보낸다
2. JwtAuthenticationFilter가 요청을 가로챈다
3. Authorization 헤더에서 토큰을 꺼낸다
4. 토큰 유효성 검사 (만료 여부, 변조 여부)
5. 유효하면 SecurityContext에 사용자 정보 저장
6. 해당 URL에 맞는 권한이 있는지 확인 (인가)
7. 권한이 있으면 요청 처리, 없으면 403 반환
```

---

## 핵심 구성 요소

| 구성 요소 | 역할 |
|-----------|------|
| SecurityConfig | 전체 Security 설정 (URL 권한, 필터 등록 등) |
| JwtTokenProvider | JWT 토큰 생성 및 검증 |
| JwtAuthenticationFilter | 모든 요청에서 토큰 검사 |
| CustomUserDetailsService | DB에서 사용자 정보를 불러오는 역할 |
| PasswordEncoder | 비밀번호 암호화 및 비교 |
| SecurityContext | 인증된 사용자 정보를 저장하는 공간 |
