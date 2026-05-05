# Spring Bean 정리

## Spring Bean이란?

Spring이 생성하고 관리하는 객체를 Bean이라고 한다.

일반 Java 객체
-> 개발자가 직접 생성

```
MemberService service = new MemberService();
```

Spring Bean
-> Spring이 자동 생성

```
@Service
public class MemberService {
}
```

---

## Bean 특징

- Spring 컨테이너가 관리
- 싱글톤 방식 사용
- 의존성 자동 주입 가능

---

## IoC (Inversion of Control)

제어의 역전

객체 생성과 관리를 개발자가 아닌 Spring이 담당한다.

기존 방식
```
MemberService service = new MemberService();
```

Spring 방식
```
@Service
public class MemberService {
}
```

Spring이 객체 생성

---

## DI (Dependency Injection)

의존성 주입

객체가 필요한 객체를 직접 생성하지 않고 주입받는다.

기존 방식
```
class Service {
    Repository repository = new Repository();
}
```

DI 적용
```
class Service {

    private Repository repository;

    public Service(Repository repository) {
        this.repository = repository;
    }
}
```

---

## Bean 등록 방법

### @Component
기본 Bean 등록

```
@Component
public class Test {
}
```

---

### @Service
비즈니스 로직 Bean

```
@Service
public class MemberService {
}
```

---

### @Repository
DB 접근 Bean

```
@Repository
public class MemberRepository {
}
```

---

### @Controller
요청 처리 Bean

```
@Controller
public class MemberController {
}
```

---

## 정리

Bean  
-> Spring이 관리하는 객체  

IoC  
-> Spring이 객체 생성 관리  

DI  
-> 필요한 객체 주입  

Bean은 IoC와 DI의 핵심 요소이다.
