# 동사와 명사의 차이점

<br />
<br />

* 리소스를 표현할 때는 명사를 사용하자
---

```
api-design을 하면서 중요한 점은
리소스 URI에 동사(리소스에 대한 작업)가 아닌 명사(리소스)를 기반으로 해야 한다는 것이다.

이글은 영어 실력이 부족해서 동사 명사가 헷갈리는 친구들에게
작은 정보를 공유하고자 작성하였다.

출처 https://learn.microsoft.com/ko-kr/azure/architecture/best-practices/api-design
```

<br />
<br />
<br />

1. 동사(Verb)

```
동사는 행동이나 상태를 나타낸다.
우리가 무언가를 할 때 사용하는 단어이다.

ex)
run (달리다), eat (먹다), sleep (자다), is (이다), are (이다)
```

<br />

```
ex)
I run every morning. (나는 매일 아침 달린다.)
She eats pizza. (그녀는 피자를 먹는다.)

동사는 "무엇을 하다" 또는 "무엇이 되다"를 묻는 질문에 답하는 단어이다.
```

<br />
<br />
<br />

2. 명사(Noun)

```
명사는 사람, 장소, 사물, 아이디어 등을 나타낸다.
쉽게 말해, 명사는 "무엇"에 대한 단어이다.

ex)
dog (개), house (집), apple (사과), happiness (행복)
```

<br />

```
I have a dog. (나는 개가 있다.)
This is a house. (이것은 집이다.)

명사는 "무엇인가?"라는 질문에 답하는 단어이다.
```

<br />
<br />
<br />

* 올바른 api-design 예시
---

```
https://adventure-works.com/orders // Good

https://adventure-works.com/create-order // Avoid

create는 만들어내는 행동을 나타낼 때 사용되는 동사이기 때문에 제외하고
복수명사인 orders만 리소스로 사용하되, http method를 post로 하면
올바른 api-design이 만들어진다.
```

<br />

| 항목        | 동사 (Verb)                           | 명사 (Noun)                             |
|-------------|--------------------------------------|-----------------------------------------|
| **정의**     | 행동이나 상태를 나타내는 단어            | 사물이나 개념을 나타내는 단어             |
| **예시**     | run (달리다), eat (먹다), sleep (자다)  | dog (개), house (집), happiness (행복)   |
| **관련 질문**| "무엇을 하다?"                          | "무엇이 있다?"                           |
