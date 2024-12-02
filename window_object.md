# Window Object

<br />
<br />

* 윈도우 객체란 무엇일까?
---

```
window 객체는 브라우저 환경에서 가장 최상위 객체로,
DOM(Document Object Model)과 BOM(Browser Object Model)의 모든 객체를 포함하는 객체이다.
```

<br />
<br />
<br />

* Dom (Document Object Model)
---

```
DOM은 "HTML" 또는 "XML" 문서의 구조를 트리(tree) 형태로 표현한 객체 모델이다.

DOM을 통해 개발자들은 JavaScript를 사용해 문서의 구조, 스타일 및 내용을 동적으로 조작할 수 있다.
```

<br />
<br />

1. document -> 현재 문서에 대한 참조.

<br />


2. history -> 브라우저의 히스토리(방문 기록) 정보를 제공.

<br />

3. location -> 현재 문서의 URL 정보.

<br />

4. navigator -> 브라우저 정보.

<br />

5. alert(), confirm(), prompt() -> 다양한 대화 상자를 표시하는 메서드.

<br />
<br />
<br />

* BOM (Browser Object Model)
---

```
BOM은 브라우저와 관련된 객체들을 포함하는 모델이다.
BOM은 문서와 직접 관련이 없지만, 브라우저 창이나 탭 전체를 조작할 수 있도록 해준다.

BOM에서의 Window 객체는 웹 페이지를 포함하는 전체 창을 나타낸다.
```

<br />
<br />

1. innerWidth, innerHeight -> 브라우저 창의 내부 너비와 높이.

<br />

2. open(), close() -> 새로운 브라우저 창을 열거나 닫는 메서드.

<br />

3. resizeTo(), resizeBy() -> 브라우저 창 크기를 조절하는 메서드.

<br />

4. scrollTo(), scrollBy() -> 페이지 내에서 스크롤하는 메서드.
