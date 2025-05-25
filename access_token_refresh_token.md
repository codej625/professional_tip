# Access Token & Refresh Token

<br />
<br />

* 인증 및 권한 관리
---

```
Access Token과 Refresh Token은 현대 웹 애플리케이션에서
사용자 인증 및 권한 관리를 효율적이고 안전하게 처리하기 위해 사용한다.

전통적인 세션 기반 인증 방식의 단점을 극복하고,
특히 Stateless API 환경에서 보안성과 사용자 경험을 동시에 향상시키는 데 중요한 역할을 한다.
```

<br />
<br />
<br />
<br />

1. Access Token

```
Access Token은 보호된 리소스(예: 사용자 정보, API 엔드포인트)에 접근할 수 있는 권한을 나타내는 짧은 수명을 가진 토큰이다.

사용자가 로그인에 성공하면 서버는 클라이언트에게 Access Token을 발급해 준다.

클라이언트는 보호된 리소스에 접근할 때마다 이 Access Token을 서버에 제시하여 자신의 권한을 증명한다.
```

<br />

`1) 짧은 수명 (Short-lived)`

```
보안상의 이유로 Access Token은 일반적으로 몇 분에서 몇 시간 정도의 짧은 유효 기간을 가진다.

이렇게 짧은 수명은 토큰이 탈취되더라도 악의적인 사용자가 오랫동안 시스템에 접근하는 것을 방지한다.
```

<br />

`리소스 접근 권한 부여`

```
Access Token 자체에 사용자의 권한 정보가 담겨 있을 수 있다.

서버는 이 토큰을 검증하여 요청한 리소스에 접근할 수 있는 권한이 있는지 확인한다.
```

<br />

`API 요청에 사용`

```
프론트엔드 애플리케이션은 백엔드 API에 데이터를 요청할 때,
HTTP 헤더(Authorization)에 Access Token을 담아 보낸다.
```

<br />
<br />
<br />

2. Refresh Token

```
Refresh Token은 Access Token의 수명이 만료되었을 때,
새로운 Access Token을 발급받기 위해 사용되는 장기적인 수명을 가진 토큰이다.

사용자가 처음 로그인할 때 Access Token과 함께 Refresh Token도 발급받게 된다.
```

<br />

`1) 긴 수명 (Long-lived)` 

```
Refresh Token은 Access Token보다 훨씬 긴 유효 기간을 가진다.

일반적으로 며칠에서 몇 달 정도의 수명을 가진다.
```

<br />

`새로운 Access Token 발급`

```
Access Token이 만료되면 클라이언트는 Refresh Token을 서버에 보내 새로운 Access Token을 요청한다.

서버는 Refresh Token의 유효성을 검증하고,
유효하다면 새로운 Access Token을 발급해 준다.
```

<br />

`사용자 재인증 최소화`

```
Refresh Token을 사용함으로써 사용자는 Access Token이 만료될 때마다 매번 로그인 과정을 다시 거칠 필요가 없어진다.

이는 사용자 경험을 크게 향상시킨다.
```

<br />
<br />
<br />

3. 저장

```
액세스 토큰은 메모리에(로컬, 세션 스토리지)

리프레시 토큰은 쿠키에 저장한다.(HttpOnly, Secure + SameSite Option 활성화 필요) 
```
