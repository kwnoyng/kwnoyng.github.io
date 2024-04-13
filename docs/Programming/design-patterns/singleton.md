---
layout: default
title: 싱글턴 패턴
parent: Design Patterns
grand_parent: Programming
has_children: false
published_date: 2024-04-13
last_modified_date: 2024-04-13
nav_order: 5
permalink: /programming/design-patterns/singleton
---

# 싱글턴 패턴

## 정의

**싱글턴 패턴(Singleton Pattern)** 이란, 클래스 인스턴스를 하나만 만들고, 그 인스턴스로의 전역 접근을 제공하는 패턴이다.

---

## 구조

&nbsp;

![싱글턴 패턴 구조](../../../assets/images/design-patterns/singleton/singleton_structure.png)

```java
public class Singleton {

    private static Singleton instance;

    private Singleton() {

    }

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }

}
```

### Singleton

- Singleton 클래스의 하나뿐인 인스턴스를 저장하는 `static`으로 선언된 정적 변수가 존재
- 생성자를 `private`으로 선언하며 외부에서 인스턴스 생성을 제한
- `static`으로 선언된 정적 메서드로부터 싱글턴 인스턴스를 생성

---

## 특징

### 장점

- **상태 정보의 중앙 관리**  
  애플리케이션의 중요 상태를 전역적으로 관리할 필요가 있을 때, 싱글턴을 사용하여 상태 정보를 중앙에서 조작하고 관리 가능
- **자원의 효율적 사용**  
  인스턴스가 단 하나만 생성되므로, 메모리 사용량을 절약하고 객체 생성에 드는 비용을 절감
- **접근 제어와 공유**  
  싱글턴으로 생성된 객체는 애플리케이션 전역에서 공유될 수 있어, 다른 객체들과의 데이터 공유가 수월
- **인스턴스 제어**  
  생성자를 private로 선언하여 외부에서 임의로 인스턴스를 생성하는 것을 방지함으로써 인스턴스의 생성을 엄격하게 통제

### 단점

- **글로벌 상태**  
  싱글턴 패턴이 글로벌 상태를 만들어내므로, 이로 인해 발생하는 의존성 문제를 야기
- **멀티스레드 환경**  
  싱글턴 생성 과정에서 동기화 처리를 제대로 하지 않을 경우, 여러 스레드가 동시에 인스턴스를 생성하려 할 때 문제가 발생하고 성능 저하를 초래

---

## 예제

### Singleton

`ChocolateBoiler`

```java
public class ChocolateBoiler {

    private boolean empty;
    private boolean boiled;
    private static ChocolateBoiler uniqueInstance;

    private ChocolateBoiler() {
        empty = true;
        boiled = false;
    }

    public static ChocolateBoiler getInstance() {
        if (uniqueInstance == null) {
            System.out.println("초콜릿 보일러 인스턴스 생성");
            uniqueInstance = new ChocolateBoiler();
        }
        System.out.println("초콜릿 보일러 인스턴스 반환");
        return uniqueInstance;
    }

    public void fill() {
        if (isEmpty()) {
            empty = false;
            boiled = false;
        }
    }

    public void drain() {
        if (!isEmpty() && isBoiled()) {
            empty = true;
        }
    }

    public void boil() {
        if (!isEmpty() && !isBoiled()) {
            boiled = true;
        }
    }

    public boolean isEmpty() {
        return empty;
    }

    public boolean isBoiled() {
        return boiled;
    }

}
```

---

## 멀티스레딩 문제 해결 방법

### Eager Initialization

```java
public class Singleton {

    private static final Singleton instance = new Singleton();

    private Singleton() {

    }

    public static Singleton getInstance() {
        return instance;
    }

}
```

- 클래스가 로딩될 때 싱글턴 인스턴스가 생성
- 멀티스레딩 환경에서 안전하고 구현이 간단
- 싱글턴 인스턴스가 사용되지 않더라도 인스턴스가 생성되므로 리소스 낭비 발생

### Static Block Initialization

```java
public class Singleton {

    private static final Singleton instance;

    static {
        try {
            instance = new Singleton();
        } catch (Exception e) {
            throw new RuntimeException("Exception occurred in creating singleton instance");
        }
    }

    private Singleton() {

    }

    public static Singleton getInstance() {
        return instance;
    }

}
```

- 클래스가 로딩될 때 정적 블록 내에서 싱글턴 인스턴스가 생성
- 초기화 과정에서 예외 처리가 가능
- 멀티스레딩 환경에서 안전
- 클래스가 로딩될 때 정적 블록 내에서 싱글톤 인스턴스가 생성

### Initialization-on-demand Holder Idiom

```java
public class Singleton {

    private Singleton() {

    }

    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }

}
```

- `getInstance()` 메서드 호출 시 내부적으로 정의된 `SingletonHolder` 클래스가 로드되며, 이때 인스턴스가 생성
- 지연 초기화 및 멀티스레딩 환경에서 안전

### Synchronized Accessor

```java
public class Singleton {

    private static Singleton instance;

    private Singleton() {

    }

    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }

}
```

- `getInstance()` 메서드가 동기화되어 있으므로 멀티스레딩 환경에서 안전
- 인스턴스를 가져올 때마다 동기화가 진행
- 동기화로 인한 성능 저하 발생

### Double-checked Locking (DCL)

```java
public class Singleton {

    private volatile static Singleton instance;

    private Singleton() {

    }

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }

}
```

- 인스턴스가 `null`일 경우에만 동기화 블럭으로 진입하며 최초 한 번만 동기화를 진행
- 동기화 블록 내에서 다시 인스턴스의 존재 여부를 체크한 후 인스턴스를 생성
- 멀티스레딩 환경에서 안전
- 불필요한 동기화를 최소화하며 성능이 향상

### Enum

```java
public enum Singleton {

    INSTANCE;

}
```

- `enum`키워드를 사용해 인스턴스를 정의
- JVM이 초기화 및 인스턴스 관리를 수행
- 멀티스레딩 환경에서 안전할 뿐만 아니라 리플렉션, 직렬화, 역직렬화 문제를 해결 가능
- 상속이 불가능하므로 확장에 제한되고 유연성이 부족

---

## 실습

### 클래스 다이어그램

&nbsp;

![싱글턴 패턴 프린터 예제](../../../assets/images/design-patterns/singleton/singleton_example_printmanager.png)

### Singleton

`PringManager`

```java
public class PrintManager {

    private static PrintManager instance;
    private Queue<String> printQueue;

    private PrintManager() {
        printQueue = new LinkedList<>();
    }

    private static class SingletonHolder {
        private static final PrintManager INSTANCE = new PrintManager();
    }

    public static PrintManager getInstance() {
        return SingletonHolder.INSTANCE;
    }

    public synchronized void addToPrintQueue(String document) {
        printQueue.offer(document);
    }

    public synchronized void printNext() {
        if (!printQueue.isEmpty()) {
            String document = printQueue.poll();
            System.out.println(Thread.currentThread().getName() + " - Print : " + document);
        } else {
            System.out.println("Print Queue is Empty !!!");
        }
    }

}
```

### Test

```java
public class Main {

    public static void main(String[] args) {
        Runnable task1 = () -> {
            PrintManager manager = PrintManager.getInstance();
            System.out.println(Thread.currentThread().getName() + " using instance HASH: " + manager.hashCode());
            manager.addToPrintQueue("기획설계서.ppt");
            manager.addToPrintQueue("개발가이드.png");
            manager.addToPrintQueue("스토리보드.pdf");
            manager.printNext();
            manager.printNext();
            manager.printNext();
        };

        Runnable task2 = () -> {
            PrintManager manager = PrintManager.getInstance();
            System.out.println(Thread.currentThread().getName() + " using instance HASH: " + manager.hashCode());
            manager.addToPrintQueue("x-lay.jpg");
            manager.addToPrintQueue("진료확인서.pdf");
            manager.addToPrintQueue("약처방전.pdf");
            manager.printNext();
            manager.printNext();
            manager.printNext();
        };

        Thread thread1 = new Thread(task1, "Thread[오영]");
        Thread thread2 = new Thread(task2, "Thread[찬]");

        thread1.start();
        thread2.start();
    }

}
```

```
// 멀티 스레딩 환경에서 동일한 싱글턴 인스턴스인지 확인
Thread[오영] using instance HASH: 1787683557
Thread[찬] using instance HASH: 1787683557

Thread[오영] - Print : 기획설계서.ppt
Thread[오영] - Print : 개발가이드.png
Thread[오영] - Print : 스토리보드.pdf

Thread[찬] - Print : x-lay.jpg
Thread[찬] - Print : 진료확인서.pdf
Thread[찬] - Print : 약처방전.pdf
```
