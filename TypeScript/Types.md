# Types

## 원시 타입

### `string`, `number`, `boolean`

자바스크립트에서 공통적으로 많이 사용되는 원시 타입이 아래 세 가지이다.

- `string`: `"Hello, world"`와 같은 문자열

- `number`: `42`와 같은 숫자, 자바스크립트에서는 `int`나 `float`가 따로 없고 모두 `number`라는 자료형으로 사용된다.

- `boolean`: `true`/`false`과 같은 참/거짓

**String, Number, Boolean이 아닌 string, number, boolean으로 사용해야 한다.**

### `Arrays`

`[1, 2, 3]`과 같은 배열 타입으로 정하고 싶을 때에는 두 가지 방법이 있다.

- `number[]`, `string[]` 등

- `Array<number>`: generics

**`[number]`는 `Tuple` 타입이므로 혼동하지 말자.**

### `any`

타입스크립트는 특별한 자료형인 `any`를 가지고 있는데, 특정한 value를 정하고 싶지 않을 때 사용하면 된다.

만약 type이 `any`라면, 해당 value의 어떤 프로퍼티에도 접근할 수 있고, 함수처럼 호출도 할 수 있으며, 다른 type의 값으로도 할당할 수 있다. (문법적으로 오류가 없다면)

- `noImplicitAny`

  특정 타입을 지정하지 않았을 때, 타입스크립트가 해당 value의 타입을 문맥으로도 추론할 수 없다면 컴파일러는 해당 value를 `any`로 지정한다.

  `noImplicitAny` 플래그를 사용하면 의도치 않게 `any` 타입이 발생할 경우 에러를 띄워준다.
