# Number, BigInt, Symbol과 데이터 타입

자바스크립트의 데이터 타입은 크게 다음과 같이 분류할 수 있다.

- Primitive

  - `undefined`: 정의되지 않음 (≠`null`)

  - `Boolean`: true / false

  - `Number`

  - `String`: 문자열

  - `BigInt`

  - `Symbol`

- Structural

  - `Object`

  - `Function`

- Structural Root Primitive

  - `null`: 값이 존재하지 않음 (≠`undefined`)

<br />

<hr />

### `Number`

ECMAScript Specification을 참조하면, Number type은 double-precision 64bit binary 형식을 따른다.

```
console.log(1 === 1.0); // true
```

- Number type은 모두 실수로 처리되는 것을 알 수 있다.

- `2^53 - 1`까지 표현할 수 있다.

<br />

### `BigInt`

BigInt Type은 Number Type의 범위를 넘어가는 숫자를 안전하게 저장하고 실행할 수 있도록 해준다.

- BigInt를 할당할 때에는 n을 붙이거나, `BigInt()`을 사용하면 된다.

  ```
  let a = 15165315641864164513n;
  let b = BigInt("46546155546513146);
  ```

- 소수는 표현할 수 없다.

- 내장 `Math`객체의 메소드를 사용할 수 없고, `Number`와 혼합해서 사용할 수 없다.

- `Number`와 함께 사용하려면 같은 자료형으로 변환해야 한다. 하지만, `BigInt`를 `Number`로 변환하면 정확성을 잃을 수 있다.

<br />

### `Symbol`

Symbol Type은 `unique`하고 `immutable`하기 때문에 주로 이름이 충돌할 위험이 없는 object의 유일한 property key를 만들 때 사용한다.

```
let key = Symbol("key");
let obj = {};
obj[key] = "text";
```

<br />
<hr />

### 데이터 타입의 필요성

```
let score = 100;
```

위 코드가 실행되면 자바스크립트 엔진은 아래와 같이 동작한다.

1. score는 특정 주소인 `addr1`을 가리키며, 그 값은 `undefined`이다.

2. 자바스크립트 엔진은 100이 Number Type인 것을 해석하고, `addr1`과 다른 주소인 `addr2`에 8byte의 메모리 공간을 확보한다.

3. `addr2`에 값 100을 저장하며, score는 `addr2`에 할당된다.

만약, 값을 참조하려고 할 때에도 한 번에 읽어야 할 메모리 공간의 크기(byte)를 알아야 한다. 자바스크립트 엔진은 Number Type의 값이 할당됐다는 것을 알고 있기 때문에 8 byte만큼만 읽는다.

결국 데이터 타입이 필요한 이유는 다음과 같다.

- 값을 저장할 때 확보해야 하는 메모리 공간의 크기를 결정하기 위해

- 값을 참조할 때 한 번에 읽어들여야 할 메모리 공간의 크기를 결정하기 위해

- 메모리에서 읽어들인 2진수를 어떻게 해석할 지 결정하기 위해
