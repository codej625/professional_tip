# iframe

<br />
<br />

* iframe?

---

```
웹페이지 안에 또 다른 웹페이지를 넣는 틀이다.

즉, 현재 페이지 안에 작은 창을 하나 만들고,
그 안에 다른 URL(다른 사이트·같은 사이트의 다른 페이지)을 띄우는 HTML 요소이다.
```

<br />
<br />
<br />
<br />

1. 언제 사용할까?

```
YouTube 영상 넣기 → youtube.com/embed/... 페이지를 iframe으로 불러와서 재생

지도 넣기 → Google Maps를 iframe으로 삽입

결제/로그인 창 → 다른 도메인 페이지를 iframe 안에서 띄우기

등 아직도 많은 서비스를 iframe으로 제공한다.
```

```
특징으로는 다른 주소(도메인)의 페이지도 그대로 보여줄 수 있다.

다만, 보안/접근 제한이 있는데 iframe 안 페이지와 바깥 페이지가
서로 DOM/스크립트에 함부로 접근하는 건 브라우저가 막는다. (cross-origin)
```

<br />
<br />
<br />

2. iframe은 넓이, 높이를 지정해주어야 한다.

```
width/height를 안 주면 브라우저마다 다르지만, 보통 300×150px 같은 작은 기본 크기로 나온다.

한쪽만 주면 나머지 한쪽은 브라우저 기본값이라 의도한 레이아웃이 안 나올 수 있기 때문에,
보통은 둘 다 지정하는 게 좋다.

특히 요즘처럼 반응형 페이지를 만들 때 유용한 예시이다.
```

```html
<div style="max-width: 800px; width: 100%; margin: 0 auto;">
  <div style="position: relative; width: 100%; height: 0;">
    <iframe 
      style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"
      src="https://www.youtube.com/embed/oPTuGyWizSM?autoplay=1&mute=1&loop=1&playlist=oPTuGyWizSM&playsinline=1" 
      title="YouTube video player" 
      frameborder="0" 
      allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
      allowfullscreen
    ></iframe>
  </div>
</div>

<!-- 바깥 div의 max-width 때문에 800px보다는 안 커짐 -->
<!-- iframe은 width, height 모두 100% 이기 때문에 그 영역을 꽉 채움 -->
```
