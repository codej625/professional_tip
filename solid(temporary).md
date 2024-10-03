# SOLID

<br /><br />

```
SOLID 원칙은 객체 지향 프로그래밍에서 코드의 품질을 높이고,
유지 보수를 쉽게 하기 위한 다섯 가지 기본 원칙을 말한다.
```

<br /><br /><br />

* Single Responsibility Principle (단일 책임 원칙)
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

* Open/Closed Principle (OCP)
---

```

```

<br />

```java

```

<br /><br /><br />

* Liskov Substitution Principle (LSP)
---

```

```

<br /><br /><br />

* Interface Segregation Principle (ISP)
---

```

```

<br />

```java

```

<br /><br /><br />

* Dependency Inversion Principle (DIP)
---

```

```

<br />

```java

```
