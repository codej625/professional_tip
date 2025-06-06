# 데이터 전달의 사용되는 여러 객체를 알아보자.

<br />
<br />

1. DTO(Data Transfer Object)

```
DTO는 데이터 전송 객체로,
주로 데이터를 전달하기 위해 사용 된다.
DTO는 일반적으로 데이터를 보유하기 위한 필드들과 이를 접근하기 위한 getter/setter 메서드들을 가진다.
주로 서비스 계층(service layer)과 웹 계층(controller layer) 간 데이터 교환에 사용된다.
(상세 분류 시 Request / Response 으로 분류가 가능하다.)
```

<br />
<br />
<br />

2. DAO (Data Access Object)

```
데이터베이스와의 상호작용을 추상화하고 데이터베이스에 접근하는 데 사용된다.
DAO는 주로 CRUD(Create, Read, Update, Delete) 연산을 수행하는 메서드들을 포함한다.

* 밑에 표를 참고하여 패키지명 분류를 참고 (일반적으로 많이 쓰이는 패키지 분류)
* Mybatis를 사용하는 프로젝트는 dao 패키지에 model, mapper를 넣어 분류하는 경우도 많음
* JPA를 사용하는 프로젝트는 domain 패키지에 entity, repository를 넣어 분류하는 경우가 많음
```

||Package(JPA)|Mybatis|JPA|
|-|-|-|-|
|Table|model or dao(entity)|DAO(UserDAO)|Entity(User)|
|Repository|dao(repository)|Mapper(UserMapper)|Repository(UserRepository)|

<br />
<br />
<br />

3. VO(Value Object)

```
값 그 자체를 표현하는 객체이다.
비즈니스 로직에서 데이터를 담는 데 사용된다.
예를 들어, 데이터베이스에서 가져온 데이터를 객체로 매핑하여 비즈니스 로직에서 사용할 수 있도록 하거나,
사용자 입력 데이터를 처리하기 위해 사용될 수 있다.

데이터 전달 객체(Data Transfer Object, DTO)와 유사하지만,
VO는 비즈니스 로직에서 값의 단위로 사용.
```

```
1) 불변성(Immutable)

값을 가지고 있지만 한 번 생성되면 내부 상태가 변경되지 않는다.
이는 객체의 상태를 안정적으로 유지하고 예측 가능하게 만든다.


2) Equality(동등성)

Value Object는 내용이 같으면 동등하다고 간주.
즉, 값이 같으면 같은 객체로 취급된다.


3)Identity(식별성)이 없음

Value Object는 주로 값 자체가 중요한 경우에 사용.
객체가 고유하게 식별되지 않고 값만으로 비교되는 경우가 많다.
```

<br />
<br />
<br />

4. Entity

```
Entity는 데이터베이스에서 엔티티(테이블)를 나타내는 객체이다.
주로 데이터베이스의 테이블과 매핑되며,
테이블의 각 열을 객체의 필드로 매핑한다.

스프링부트에서 Entity는 주로 JPA(Java Persistence API)를 사용하여 정의되고,
@Entity 어노테이션을 클래스 위에 붙여서 JPA에게 이 클래스가 엔티티임을 알린다.

Entity 클래스는 데이터베이스 테이블의 구조를 정의,
JPA를 통해 데이터베이스와 상호작용할 때 사용된다.
```
