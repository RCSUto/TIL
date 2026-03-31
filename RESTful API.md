# RESTful API 정리

## 1. RESTful API란?
REST(Representational State Transfer) 원칙을 따르는 API

- HTTP 기반으로 동작
- 자원을 URI로 표현

---

## 2. 특징

### 자원 기반 설계
- URL로 자원을 표현
ex) /users, /posts

---

### HTTP Method 사용
+ GET : 조회
+ POST : 생성
+ PUT : 수정
+ DELETE : 삭제

---

### 무상태성 (Stateless)
- 요청 간 상태를 저장하지 않음

---

### 클라이언트-서버 구조
- 역할 분리로 독립성 확보

---

## 3. 장점
+ 구조가 단순
+ 확장성 좋음
+ 다양한 플랫폼 지원

---

## 4. 예시

GET /users → 사용자 조회  
POST /users → 사용자 생성  
DELETE /users/1 → 사용자 삭제
