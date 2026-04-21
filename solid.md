# 객체지향 SOLID 원칙 정리

SOLID는 객체지향 설계 5가지 원칙이다.

S -> 단일 책임 원칙  
O -> 개방 폐쇄 원칙  
L -> 리스코프 치환 원칙  
I -> 인터페이스 분리 원칙  
D -> 의존 역전 원칙  

---

## 1. SRP (단일 책임 원칙)

하나의 클래스는 하나의 책임만 가져야 한다.

잘못된 예
```
class UserService {
    void save() {}
    void login() {}
    void sendEmail() {}
}
```

개선
```
UserService
EmailService
LoginService
```

---

## 2. OCP (개방 폐쇄 원칙)

확장에는 열려 있고 수정에는 닫혀 있어야 한다.

잘못된 예
```
if(type == "A") {}
else if(type == "B") {}
```

개선
```
interface Payment {
    void pay();
}
```

---

## 3. LSP (리스코프 치환 원칙)

자식 클래스는 부모 클래스를 대체할 수 있어야 한다.

예시
```
Animal a = new Dog();
```

위반 예
```
class Bird {
    void fly(){}
}

class Penguin extends Bird {
    void fly() {}
}
```

---

## 4. ISP (인터페이스 분리 원칙)

인터페이스는 작게 나누어야 한다.

잘못된 예
```
interface Animal {
    eat();
    fly();
    swim();
}
```

개선
```
interface Flyable {}
interface Swimable {}
```

---

## 5. DIP (의존 역전 원칙)

구체 클래스가 아닌 추상화에 의존해야 한다.

잘못된 예
```
class Service {
    Repository repository = new Repository();
}
```

개선
```
class Service {
    Repository repository;
}
```

---

## 정리

SRP -> 하나의 책임  
OCP -> 확장 가능  
LSP -> 부모 대체 가능  
ISP -> 인터페이스 분리  
DIP -> 추상화 의존  
