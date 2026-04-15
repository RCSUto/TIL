# Spring

## 1. Spring MVC 패턴

### Spring MVC란?
Spring MVC는 Model - View - Controller 구조로 웹 애플리케이션을 개발하는 패턴이다.  
역할을 분리하여 유지보수성과 확장성을 높인다.

동작 흐름  
Client -> Controller -> Service -> Repository -> DB  
Client <- View <- Controller  

---

### Controller
사용자의 요청을 처리하는 계층

- URL 요청 받음
- Service 호출
- 결과 View 전달

ex)
```
@Controller
public class BoardController {

    @GetMapping("/board")
    public String board() {
        return "board";
    }
}
```

---

### Model
Controller에서 View로 전달하는 데이터

ex)
```
model.addAttribute("list", boardList);
```

---

### View
사용자에게 보여지는 화면

+ HTML

+ Thymeleaf

+ JSP

ex)
```
board.html
```

---

### Spring MVC 동작 과정

1. 사용자가 요청
2. Controller 요청 수신
3. Service 로직 처리
4. Repository DB 접근
5. 결과 반환
6. View 데이터 전달
7. 화면 출력

---

## 2. Spring 게시판에 사용되는 기술

### Spring Boot
Spring 기반 웹 프레임워크

+ 내장 서버

+ 자동 설정

+  빠른 개발

---

### Spring MVC
요청 처리 구조

Controller → Service → Repository

---

### JPA (Hibernate)
객체와 DB를 매핑하는 기술

ex)
```
@Entity
public class Board {

    @Id
    @GeneratedValue
    private Long id;

    private String title;
}
```

---

### Spring Data JPA
Repository 자동 구현

ex)
```
public interface BoardRepository
        extends JpaRepository<Board, Long> {
}
```

---

### Thymeleaf
HTML 템플릿 엔진

ex)
```
<tr th:each="board : ${list}">
    <td th:text="${board.title}"></td>
</tr>
```

---

### Lombok
Getter / Setter 자동 생성

ex)
```
@Getter
@Setter
@NoArgsConstructor
```

---

### Database
게시판 데이터 저장

+ MySQL

+ MariaDB

+ H2

---

### Validation
입력값 검증

ex)
```
@NotBlank
private String title;
```

---

## 3. Spring 계층 구조

Spring 게시판 구조

Controller -> Service -> Repository -> Database  

---

### Entity
DB 테이블과 매핑되는 클래스

ex)
```
@Entity
public class Board {

    @Id
    @GeneratedValue
    private Long id;

    private String title;
    private String content;
}
```

역할
+ 테이블 구조 정의

+ DB 데이터 저장

---

### Controller
요청 처리 담당

ex)
```
@Controller
public class BoardController {

    @GetMapping("/board")
    public String board() {
        return "board";
    }
}
```

역할
+ 요청 받기

+ Service 호출

+ View 반환

---

### Service
비즈니스 로직 처리

ex)
```
@Service
public class BoardService {

    private final BoardRepository repository;

    public BoardService(BoardRepository repository) {
        this.repository = repository;
    }

    public List<Board> getList() {
        return repository.findAll();
    }
}
```

역할
+ 로직 처리

+ Controller 와 Repository 연결

---

### Repository
DB 접근 담당

ex)
```
@Repository
public interface BoardRepository
        extends JpaRepository<Board, Long> {
}
```

역할
+ CRUD 처리

+ DB 접근

---

### DTO
데이터 전달 객체

ex)
```
public class BoardDTO {

    private String title;
    private String content;
}
```

역할
+ 화면 데이터 전달

+ Entity 대신 사용

---

## 정리

Spring MVC  
-> Controller / Model / View 구조  

Spring 게시판 기술  
-> Spring Boot / MVC / JPA / Thymeleaf / Lombok / DB  

Spring 계층 구조  
+ Controller : 요청 처리  

+ Service : 로직 처리  

+ Repository : DB 접근  

+ Entity : DB 테이블  

+ DTO : 데이터 전달
