# Spring 4대 개념 정리

Spring의 핵심 개념은 DI, IoC, PSA, AOP 이다.  
Spring이 객체를 관리하고 기능을 분리하는 핵심 원리이다.

---

## 1. DI (Dependency Injection)

DI는 의존성 주입이다.  
객체가 직접 다른 객체를 생성하지 않고 외부에서 주입받는 방식이다.

기존 방식 (강한 결합)
```
class Service {
    Repository repository = new Repository();
}
```

DI 적용 (느슨한 결합)
```
class Service {

    private Repository repository;

    public Service(Repository repository) {
        this.repository = repository;
    }
}
```

장점
- 객체 간 결합도 감소
- 테스트 쉬움
- 유지보수 쉬움

---

## 2. IoC (Inversion of Control)

IoC는 제어의 역전이다.  
객체 생성과 관리를 Spring이 담당한다.

기존 방식
-> 개발자가 객체 생성

Spring 방식
-> Spring 컨테이너가 객체 생성

예시
```
@Service
public class BoardService {
}
```

Spring이 객체 생성 후 관리한다.

특징
- 객체 생성 자동
- 의존성 자동 주입
- Bean 관리

---

## 3. PSA (Portable Service Abstraction)

PSA는 서비스 추상화이다.  
기술이 바뀌어도 코드 수정 없이 사용 가능하게 하는 것.

예시
```
JdbcTemplate jdbcTemplate;
```

DB 변경
MySQL -> Oracle -> H2

코드 수정 없음

예시
```
@Transactional
```

장점
- 기술 변경 쉬움
- 코드 수정 최소화
- 이식성 증가

---

## 4. AOP (Aspect Oriented Programming)

AOP는 관점 지향 프로그래밍이다.  
공통 기능을 따로 분리하여 적용하는 방식이다.

공통 기능
- 로그
- 트랜잭션
- 권한 검사
- 예외 처리

예시
```
@Around("execution(* com.example..*(..))")
public Object log() {
}
```

장점
- 중복 코드 제거
- 유지보수 쉬움
- 공통 기능 분리

---

## 정리

DI -> 의존성 주입  
IoC -> 제어의 역전  
PSA -> 서비스 추상화  
AOP -> 공통 기능 분리
