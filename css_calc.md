# CSS "calc"

<br />
<br />

* calc란?
---

```
calc(100%)는 요소의 100% 크기를 의미한다.

calc(100% - 40px) 이런 식으로 사용한다면 100%의 40px만큼만 제하겠다는 뜻이다.

이 방법을 사용하면 요소의 별도크기가 없어도 화면에 딱 맞게 조정할 수 있다.
```

<br />
<br />
<br />
<br />

1. 예시

```
자식 컴포넌트인 Box 들의 크기를
부모 요소 안에서 빈 공간만큼만 채우려면,
부모 요소의 값이 정해져 있어야 한다.

이럴 때 px을 쓰지 않고 (수치를 정해놓지 않고)
빈 공간만큼 calc()를 사용하면,
부모 컴포넌트를 꽉 채울 수 있다.
```

```jsx
<Box sx={{
      flexGrow: 1,
      mt: 1,
      display: "flex",
      height: "calc(100%)"
}}>
  <Box
    sx={{
      flexGrow: 1,
      textAlign: "center",
      border: "1px solid #000",
      borderRadius: 2,
    }}
  >
    1
  </Box>
  <Box
    sx={{
      flexGrow: 1,
      textAlign: "center",
      border: "1px solid #000",
      borderRadius: 2,
    }}
  >
    2
  </Box>
  <Box
    sx={{
      flexGrow: 1,
      textAlign: "center",
      border: "1px solid #000",
      borderRadius: 2,
    }}
  >
    3
  </Box>
</Box>
```
