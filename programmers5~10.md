# Java TIL (객체지향 심화 - 상세 정리)

## 27. 클래스와 객체

### 클래스란?
객체를 생성하기 위한 설계도

+ 필드(데이터) + 메서드(기능)로 구성됨

ex)
```
class Person {
    String name;
    int age;

    void introduce() {
        System.out.println("이름: " + name);
    }
}
```

### 객체 생성
클래스를 기반으로 실제 데이터를 가진 인스턴스를 만든다.

ex)
```
Person p = new Person();
p.name = "Tom";
p.introduce();
```

### 참조 타입
객체의 주소를 저장하는 타입

ex)
```
Person p1 = new Person();
Person p2 = p1; // 같은 객체 참조
```

### String 클래스
문자열을 다루는 클래스

ex)
```
String str = "Hello";

System.out.println(str.length()); // 길이
System.out.println(str.substring(0, 2)); // He
System.out.println(str.equals("Hello")); // true
```

### 필드
객체의 상태를 저장

ex)
```
class Car {
    String color;
    int speed;
}
```

### 메서드
객체의 기능 정의

ex)
```
void run() {
    System.out.println("달린다");
}
```

### static과 scope

ex)
```
class Test {
    static int count = 0;

    void increase() {
        count++;
    }
}
```
+ static → 객체 없이 사용 가능

+ scope → 변수의 사용 범위

### enum (열거형)

ex)
```
enum Day {
    MON, TUE, WED
}
```

---

## 28. 클래스 다듬기

### 생성자

ex)
```
class Person {
    String name;

    Person(String name) {
        this.name = name;
    }
}
```

### this
현재 객체를 가리킴

### 메서드 오버로딩

ex)
```
int add(int a, int b) {
    return a + b;
}

double add(double a, double b) {
    return a + b;
}
```

### 패키지
클래스를 그룹으로 관리

ex)
```
package com.example;
```

---

## 29. 상속

### 상속

ex)
```
class Animal {
    void sound() {
        System.out.println("소리");
    }
}

class Dog extends Animal {
}
```

### 오버라이딩

ex)
```
class Dog extends Animal {
    void sound() {
        System.out.println("멍멍");
    }
}
```
### super

ex)
```
class Dog extends Animal {
    Dog() {
        super();
    }
}
```

### 접근제한자
+ public

+ protected

+ default

+ private

### 추상 클래스

ex)
```
abstract class Animal {
    abstract void sound();
}
```

### 형변환

ex)
```
Animal a = new Dog(); // 업캐스팅
Dog d = (Dog) a;      // 다운캐스팅
```

---

## 30. 인터페이스

### 인터페이스 정의

ex)
```
interface Animal {
    void sound();
}
```

### 구현

ex)
```
class Dog implements Animal {
    public void sound() {
        System.out.println("멍멍");
    }
}
```

### default method

ex)
```
interface Test {
    default void hello() {
        System.out.println("기본 메서드");
    }
}
```

### 익명 클래스

ex)
```
Animal a = new Animal() {
    public void sound() {
        System.out.println("익명");
    }
};
```

---

## 31. 예외 처리

### try-catch

ex)
```
try {
    int a = 10 / 0;
} catch(Exception e) {
    System.out.println("에러 발생");
}
```
### throws

ex)
```
void test() throws Exception {
}
```

### 사용자 정의 예외

ex)
```
class MyException extends Exception {
}

throw new MyException();
```
