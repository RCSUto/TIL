# Spring 어노테이션 정리

## 어노테이션이란?

어노테이션은 클래스나 메서드에 추가 정보를 제공하는 문법이다.  
Spring이 해당 정보를 읽고 동작을 수행한다.

형태
```
@Controller
@Service
@Repository
```

---

## 주요 Spring 어노테이션

### @Controller
요청 처리 클래스 지정

```
@Controller
public class BoardController {
}
```

---

### @Service
비즈니스 로직 클래스 지정

```
@Service
public class BoardService {
}
```

---

### @Repository
DB 접근 클래스 지정

```
@Repository
public class BoardRepository {
}
```

---

### @Entity
DB 테이블 매핑

```
@Entity
public class Board {
}
```

---

### @Autowired
의존성 자동 주입

```
@Autowired
BoardService service;
```

---

### @GetMapping
GET 요청 처리

```
@GetMapping("/board")
```

---

### @PostMapping
POST 요청 처리

```
@PostMapping("/write")
```

---

## 정리

어노테이션이란  
-> Spring이 동작하도록 정보를 제공하는 표시

@Controller -> 요청 처리  
@Service -> 로직 처리  
@Repository -> DB 접근  
@Entity -> DB 매핑  
@Autowired -> DI 주입  
@GetMapping -> GET 요청  
@PostMapping -> POST 요청  
