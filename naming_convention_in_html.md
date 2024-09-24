# HTML Naming convention

<br /><br />

```
HTML에서 표준으로 사용하는 명명규칙을 알아보자.
(선택자, CSS 작성 순서)
```

<br /><br /><br />

1. ID
---

```
1) 시작의 이름은 영문 소문자를 사용하되 두 번째 단어부터 첫 번째 문자를 대문자로 처리하는 기법(카멜 케이스)을 사용한다.

2) 동일한 이름의 id는 문서에서 한번만 사용이 가능하다.

3)지정된 레이아웃 및 폼객체를 제외한 스타일 지정을 위하여 id를 사용하지 않는다.

4) 앵커로서 사용되는 엘리먼트는 id 지정이 가능하다.

5) User Interface 동작을 위하여 DOM 선택자로서의 id 지정은 가능하다.

6) 예약어가 있는 경우 예약어를 사용한다.
```

<br />

| 잘못된 예      | 올바른 예     |
|----------------|---------------|
| error_Mesage   | errorMesage   |
| control_center | controlCenter |

<br /><br />

2. Class
---

```
1) 네이밍은 - (하이폰) 사용을 기본으로 한다.
(클래스 네임이 서버사이드 스크립트의 변수와 자바스크립트의 변수 정의시 혼돈을 방지)

2) 시작의 이름은 영문 소문자를 사용하며 두 번째 단어부터 하이픈(-)으로 연결하며 대문자는 사용하지 않는다.

3) 영문 소문자, 숫자, 하이픈(-)만 사용할 수 있다.

4) 하이픈(-)은 2개 이상의 단어를 조합할 때만 사용한다.

5) 하이픈(-)을 이용하여 3단계를 초과하여 사용하는 방법은 지양한다.

6) 숫자 없으면 '1'이라는 숫자가 생략된 것으로 간주한다.

7) 다중의 클래시 지정을 허용하며 최대 3개 이상 사용하지 않도록 주의한다.

8) 단, 여백 혹은 넓이를 지정하는 클래스는 예외로 한다.
```

<br />

| 잘못된 예      | 올바른 예     |
|----------------|---------------|
| error_Mesage   | error-mesage   |
| control_center | control-center |

<br /><br />

3. CSS 작성순서

<br />

| 순서 | 속성           | 속성 값                                                                                               |
|------|----------------|-----------------------------------------------------------------------------------------------------|
| 1    | display        | none, block, inline, inline-block, inline-table, list-item, table, table-caption, table-cell, table-column, table-column-group, table-footer-group, table-header-group, table-row, table-row-group |
| 2    | visibility     | visible, hidden, collapse                                                                             |
| 3    | overflow       | visible, hidden, scroll, auto                                                                         |
| 4    | float          | left, right, none                                                                                    |
| 5    | position       | absolute, fixed, relative, static                                                                     |
| 6    | width          | auto, length(px), %, max-width, min-width                                                            |
| 7    | height         | auto, length(px), %, max-height, min-height                                                          |
| 8    | margin         | auto, length(px, em), %                                                                              |
| 9    | padding        | auto, length(px, em), %                                                                              |
| 10   | border         | border-width, border-style, border-color                                                              |
| 11   | background     | background-color, background-image, background-repeat, background-attachment, background-position      |
| 12   | font           | font-style, font-variant, font-weight, font-size/line-height, font-family                           |
| 13   | color          | hex_number, hex_number                                                                                |
|      | letter-spacing  | normal, length                                                                                       |
|      | text-decoration | none, underline, overline, line-through, blink                                                       |
|      | text-align     | left, right, center, justify                                                                          |
|      | white-space    | normal, nowrap, pre, pre-line, pre-wrap                                                              |
|      | word-spacing    | normal, length(px, em, etc)                                                                           |
| 14   | 기타           | 여기서 언급하지 않은 나머지 속성들은 폰트 속성 이후에 선언하며, 선언 순서는 무관하다.                  |
