# SOLID

<br /><br />

```
SOLID 원칙은 객체 지향 프로그래밍에서 코드의 품질을 높이고,
유지 보수를 쉽게 하기 위한 다섯 가지 기본 원칙을 말한다.
```

<br /><br /><br />

* Single Responsibility Principle (SRP) (단일 책임 원칙)
---

```
객체는 단 하나의 책임만 가져야 한다는 원칙을 말한다.

여기서 '책임' 이라는 의미는 하나의 '기능 담당'으로 보면 된다.
(즉, 하나의 클래스는 하나의 기능 담당하여 하나의 책임을 수행하는데 집중되어야 있어야 한다는 의미이다.)
```

<br /><br />

1. SRP를 위반한 예시

<br />

```java
public class UserManager {
    // 하나의 클래스가 여러 책임을 가지고 있다.
    public void createUser(String username, String password) {
        // 사용자 생성 로직
        System.out.println("Creating user: " + username);
    }

    public void sendEmail(String email) {
        // 이메일 전송 로직
        System.out.println("Sending email to: " + email);
    }

    public void logActivity(String activity) {
        // 활동 기록 로직
        System.out.println("Logging activity: " + activity);
    }

}
```

<br />

```
위의 UserManager 클래스는 사용자 생성, 이메일 전송, 활동 기록이라는 서로 다른 책임을 가지고 있다.
(이로 인해 클래스가 변경될 때 관련 없는 코드가 영향을 받을 수 있다.)
```

<br /><br />

2. SRP를 준수하는 예시

<br />

```java
public class User {
    private String username;
    private String password;

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

    public String getUsername() {
        return username;
    }
}

public class UserService {
    public User createUser(String username, String password) {
        // 사용자 생성 로직
        System.out.println("Creating user: " + username);
        return new User(username, password);
    }
}

public class EmailService {
    public void sendEmail(String email) {
        // 이메일 전송 로직
        System.out.println("Sending email to: " + email);
    }
}

public class ActivityLogger {
    public void logActivity(String activity) {
        // 활동 기록 로직
        System.out.println("Logging activity: " + activity);
    }
}
```

<br />

```
이렇게 클래스들을 하나의 책임으로 나누면, 
각 클래스가 독립적으로 변경되거나 확장될 수 있으며, 
코드의 가독성과 유지보수성이 크게 향상 된다.
```

<br /><br /><br />

* Open/Closed Principle (OCP) (개방/폐쇄 원칙)
---

```
각 클래스가 확장에는 열려 있어야 하고,
변경에는 닫혀 있어야 한다는 원칙이다.
(클래스를 수정해서 사용하지 말고 확장해서 사용 하라는 뜻)
```

<br /><br />

1. OCP를 위반한 예시

<br />

```java
public class ReportGenerator {
    
    public void generateReport(String type) {
        if (type.equals("PDF")) {
            System.out.println("Generating PDF report...");
        }
        else if (type.equals("HTML")) {
            System.out.println("Generating HTML repot...");
        }
    }

}
```

<br />

```
ReportGenerator 클래스의 generateReport 메서드는
인자로 "PDF" 혹은 "HTML"이 주어지면 이를 기준으로
if 문을 실행해서 그에 해당하는 형식으로 문서를 생성한다.

하지만 이후 "XML" 등 다른 형식으로 문서를 생성할 필요가 생기면
메서드 자체를 수정해야 한다.

그 과정에서 실수로 기존 과정에 영향을 주게 될 수도 있다.
```

<br /><br />

2. OCP를 준수하는 예시

<br />

```java
// 문서를 생성하는 기능을 interface로 추상 메서드만 만들어 놓는다.
public interface Report {
    void generate();
}

// Report 인터페이스를 오버라이드해서 각자의 방식으로 문서를 생성하는 책임을 수행
public class PDFReport implement Report {
    @Override
    public void generate() {
        System.out.println("Generating PDF report...");
    }
}
public class HTMLReport implement Report {
    @Override
    public void generate() {
        System.out.println("Generating HTML report...");
    }
}
// 새로운 형식의 문서가 필요하면 인터페이스를 적용한 다른 클래스를 만들면 된다.
public class XMLReport implement Report {
    @Override
    public void generate() {
        System.out.println("Generating XML report...");
    }
}
```

<br />

```
코드를 수정하는 게 아닌 확장함으로써,
코드를 수정하는 과정에서 발생할 수 있는 문제들로부터 자유로운 설계를 할 수 있다.
```

<br /><br /><br />

* Liskov Substitution Principle (LSP) (리스코프 치환 원칙)
---

```
자식 클래스는 언제나
부모 클래스를 대체할 수 있어야 한다는 원칙이다.
(쉽게 설명하면 자식은 최소한 부모가 하는 일은 다 해야 한다는 것)
```

<br /><br />

1. LSP를 위반한 예시

<br />

```java

```

<br />

```

```

<br /><br />

2. LSP를 준수하는 예시

<br />

```java

```

<br />

```

```

<br /><br /><br />

* Interface Segregation Principle (ISP)
---

```

```

<br /><br />

1. ISP를 위반한 예시

<br />

```java

```

<br />

```

```

<br /><br />

2. ISP를 준수하는 예시

<br />

```java

```

<br />

```

```

<br /><br /><br />

* Dependency Inversion Principle (DIP)
---

```

```

<br /><br />

1. DIP를 위반한 예시

<br />

```java

```

<br />

```

```

<br /><br />

2. DIP를 준수하는 예시

<br />

```java

```

<br />

```

```
