# Spring Security / JWT 정리

## Spring Security란?

Spring Security는 Spring에서 제공하는 보안 프레임워크이다.  
로그인, 로그아웃, 권한 관리, 인증 등을 처리한다.

주요 기능
- 로그인 인증
- 권한 관리
- 비밀번호 암호화
- 세션 관리
- CSRF 보호

ex)
```
@Configuration
@EnableWebSecurity
public class SecurityConfig {
}
```

Spring Security를 사용하면 보안 관련 기능을 쉽게 구현할 수 있다.

---

## 인증(Authentication)

사용자가 누구인지 확인하는 과정이다.

ex)
- 아이디 로그인
- 비밀번호 확인
- OTP 인증

```
아이디: admin
비밀번호: 1234
```

정보가 맞으면 로그인 성공

---

## 인가(Authorization)

인증된 사용자가 특정 기능을 사용할 권한이 있는지 확인하는 과정이다.

ex)
- 관리자만 삭제 가능
- 일반 사용자는 조회만 가능

```
ROLE_ADMIN
ROLE_USER
```

---

## JWT란?

JWT(JSON Web Token)는 사용자 정보를 담은 토큰 방식 인증 기술이다.

구조
Header -> 토큰 정보  
Payload -> 사용자 정보  
Signature -> 위조 방지 서명  

---

## JWT 동작 과정

1. 사용자가 로그인 요청
2. 서버가 사용자 확인
3. JWT 토큰 발급
4. 클라이언트 저장
5. 요청 시 토큰 전달
6. 서버가 토큰 검증

---

## JWT 예시

```
eyJhbGciOiJIUzI1Ni...
```

장점
- 서버 세션 저장 불필요
- 확장성 좋음
- 모바일 환경 적합

단점
- 토큰 탈취 위험
- 만료 전 강제 삭제 어려움
