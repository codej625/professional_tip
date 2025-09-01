# 공통함수 만들기

<br />
<br />

* 공통함수를 만들 때 신경 써야 할 것들

---

```
공통함수는 개인뿐만 아니라
팀원 전체가 재사용하기 위한 목적이 큰 함수이다.

그렇기 때문에 사용 방법은 물론이고,
유지보수에 있어 여러 가지 고려를 해야 한다.

어떤 원칙을 지키면서 공통함수를 만들어야
잘 만들 수 있는지 확인해 보자.
```

<br />
<br />
<br />
<br />

1. 주의사항

`단일 책임 원칙 (Single Responsibility Principle, SRP)`

- 함수는 한 가지 기능만 수행해야 하며, 그 기능을 잘 수행해야 한다. (여러 작업을 하나의 함수에 혼합하지 말자)

<br />

`명확한 명명`

- 함수의 목적을 명확히 나타내는 이름을 사용한다. (calculateAverage 대신 calc 사용 지양)
- 언어에 맞는 명명 규칙을 따르자

<br />

`모듈화와 재사용성`

- 여러 상황에서 재사용할 수 있도록 함수를 충분히 일반화한다.
- 특정 모듈이나 컨텍스트에 강하게 의존하지 않도록 설계해야 한다.

<br />

`문서화`

- 함수의 역할, 매개변수, 반환값을 주석 또는 JSDoc/TypeScript 주석으로 명확히 설명한다.
- 특히 공통함수는 여러 개발자가 사용할 가능성이 높으므로 문서화가 중요하다.

<br />

`에러 처리`

- 예상 가능한 에러를 처리하고, 호출자에게 명확한 에러 메시지를 제공한다.

<br />
<br />
<br />

2. 예시

```ts
/**
 * 숫자 배열의 평균을 계산합니다.
 * @param numbers 숫자 배열
 * @returns 평균값 (빈 배열일 경우 0 반환)
 */
function calculateAverage(numbers: number[]): number {
  if (!Array.isArray(numbers) || numbers.length === 0) {
    return 0;
  }
  const sum = numbers.reduce((acc, num) => acc + num, 0);
  return sum / numbers.length;
}

// 사용 예시
const scores: number[] = [85, 90, 95];
const average: number = calculateAverage(scores);
console.log(`평균: ${average}`); // 90
```

```
* 원칙 적용

단일 책임 - 평균 계산만 수행.
명확한 명명 - calculateAverage는 목적을 명확히 전달.
타입 안정성 - numbers: number[]와 반환 타입 number 명시.
에러 처리 - 빈 배열이나 잘못된 입력 처리.
문서화 - JSDoc으로 함수 설명 제공.
```

<br />
<br />
<br />

3. 팁

`테스트 작성`

- Jest나 Mocha 같은 도구로 함수 단위 테스트를 작성해 안정성을 보장한다.

<br />

`성능 고려`

- 공통함수는 자주 호출되므로, 연산 비용이 큰 작업은 최적화한다.

<br />

`버전 관리`

- GitHub에서 관리할 경우, README에 함수 목록과 사용 예시를 포함한다.

<br />

`모듈화`

- 공통함수는 별도의 유틸리티 파일에 모아 export/import로 사용한다.

```ts
// utils.ts

/**
 * 숫자 배열의 평균을 계산
 */
export function calculateAverage(numbers: number[]): number {
  ...
}

/**
 * 객체 배열을 조건으로 필터링
 */
export function filterItems<T>(items: T[], predicate: (item: T) => boolean): T[] {
  ...
}

/**
 * 문자열을 특정 포맷으로 변환
 */
export function formatString(input: string, format: 'upper' | 'lower' | 'capitalize'): string {
  ...
}


// main.ts

import { calculateAverage, formatString } from './utils';

const scores = [85, 90, 95];
console.log(calculateAverage(scores)); // 90

const text = 'hello world';
console.log(formatString(text, 'capitalize')); // Hello world
```
