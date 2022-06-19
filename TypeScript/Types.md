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

## 타입 표기

### 변수에 대한 타입 표기

`const`와 `let`으로 변수를 선언할 때 변수의 타입을 명시적으로 지정하기 위해 타입을 표기할 수 있으며, 선택사항이다.

```
let myName: string = "Alice";
```

하지만 대부분의 경우 타입 표기는 불필요하다. TypeScript는 자동으로 코드 내 타입들을 추론하기 때문이다.

```
// 여기서 'myName'은 'string' 타입으로 추론된다.
let myName = "Alice";
```

### 함수에 대한 타입 표기

함수는 데이터를 주고 받는 주요 수단이며, TypeScript에서는 함수의 입/출력값의 타입을 지정할 수 있다.

#### 매개변수의 타입 표기

함수를 선언할 때 함수가 허용할 매개변수의 탕비을 선언하기 위해 각 매개변수 뒤에 타입을 표기할 수 있다.

매개변수 타입은 매개변수 이름 뒤에 표기한다.

```
function greet(name: string) {
  console.log("Hello, " + name.toUpperCase() + "!!");
}

// 화살표 함수에서도 사용 가능하다.
const greet = (name: string) => {
  console.log("Hello, " + name.toUpperCase() + "!!")
}
```

#### 반환 타입 표기

반환 타입도 표기가 가능하며, 매개변수 목록 뒤에 표기한다.

```
function getFavoriteNumber(): number {
  return 28;
}
```

변수 타입 표기와 마찬가지로, 반환 타입도 표기하지 않아도 되는 것이 일반적이다.

TypeScript에서 해당 함수에 들어있는 return문을 바탕으로 반환값의 타입을 추론하기 때문이다.

코드의 잘못된 수정을 미연에 방지하거나, 문서화를 목적으로 타입 표기를 수행하는 경우가 있다.

### 객체 타입 표기

원시 타입을 제외하고 가장 많이 사용하는 타입은 객체 타입이다.

객체 타입을 정의하기 위해서는, 해당 객체의 프로퍼티들과 각 프로퍼티들의 타입들을 나열하면 된다.

```
function printCoord(pt: { x: number; y: number }) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
printCoord({ x: 3, y: 7});
```

각 프로퍼티를 구분할 때 `,`, `;`를 사용할 수 있고, 가장 마지막 구분자의 표기는 선택사항이다.

각 프로퍼티의 타입 표기 또한 선택사항인데, 타입 미지정시 해당 프로퍼티는 `any`타입으로 간주한다.

#### 옵셔널 프로퍼티

객체 타입은 일부 또는 모든 프로퍼티의 타입을 선택적 타입, 즉 옵셔널로 지정할 수 있으며 프로퍼티 이름 뒤에 `?`를 붙이면 된다.

```
function printName(obj: { first: string; last?: string }) {
  // ...
}

printName({ first: "Bob" });
printName({ first: "Alice", last: "Alisson" })
```

JS에서는 존재하지 않는 프로퍼티에 접근하면 런타임 오류가 발생하지 않고 `undefined` 값을 얻게 된다.

따라서 옵셔널 프로퍼티를 읽었을 때 해당 값을 사용하려면 그 전에 프로퍼티의 값이 `undefined`인지 여부를 확인해야 한다.

```
function printName(obj: { first: string; last?: string }) {
  console.log(obj.last.toUpperCase()) // obj.last가 제공되지 않으면 프로그램이 멈추기 때문에

  if (obj.last !== undefined) {
    console.log(obj.last.toUpperCase());
  }
  // 또는
  console.log(obj.last?.toUpperCase())
  // 코드로 프로퍼티의 값이 undefined인지 확인해야 한다.
}
```

### 유니언 타입

TypeScript의 타입 시스템에서는 기존의 타입을 기반으로 다양한 연산자를 사용하여 새로운 타입을 만들 수 있다.

#### 유니언 타입 정의하기

타입을 조합하는 첫 번째 방법은 서로 다른 두 개 이상의 타입들을 사용해서 만드는 유니언 타입이다.

유니언 타입 조합에 사용된 타입 중 하나를 타입으로 가질 수 있으며, 조합에 사용된 각 타입을 유니언 타입의 멤버라고 부른다.

```
function printId(id: number | string) {
  console.log("Your ID is: " + id);
}
printId(101); // OK
printId("202") // OK
printId({ myId: 22342 }); // 오류
```

#### 유니언 타입 사용하기

TypeScript에서 유니언을 다룰 때에는 해당 유니언 타입의 모든 멤버에 대해서 유효한 작업일 때에만 허용된다.

`string | number` 타입인 경우, `string` 타입에만 유효한 method는 사용이 불가능하다.

```
function printId(id: number | string) {
  console.log(id.toUpperCase()); // number인 경우 사용 불가능하기 때문에 오류 발생
}
```

이를 해결하기 위해서 다음과 같이 코드를 수정할 수 있다.

```
function printId(id: number | string) {
  if (typeof id === "string") { // string 타입
    console.log(id.toUpperCase())
  } else { // number 타입
    console.log(id)
  }
}
```

### 타입 별칭

똑같은 타입을 한 번 이상 재사용하거나 또 다른 이름으로 부르고 싶은 경우도 존재한다.

```
type Point = {
  x: number;
  y: number;
}

function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}

printCoord({ x: 100, y: 100 });
```

타입 별칭을 사용하면 유니언 타입을 포함한 모든 타입에 대해 새로운 이름을 부여할 수 있다.

```
type Id = number | string;
```

### 인터페이스

객체 타입을 만들기 위한 또 다른 방법은 인터페이스를 선언하는 것이다.

```
interface Point {
  x: number;
  y: number;
}
```

### 타입 별칭과 인터페이스의 차이점

타입 별칭과 인터페이스는 유사하고, 둘 중 하나를 자유롭게 선택해서 사용할 수 있다.

`interface`가 가지는 대부분의 기능은 `type`에서도 동일하게 사용할 수 있지만, 차이점이 있다면

타입은 새 프로퍼티를 추가할 수 없도록 되어있지만, 인터페이스의 경우 확장될 수 있다는 것이다.

```
// 인터페이스 확장하기
interface Animal {
  name: string
}

interface Bear extends Animal {
  honey: boolean
}

const bear = getBear()
bear.name
bear.honey

// 타입 확장하기 (교집합을 통해)
type Animal = {
  name: string
}

type Bear = Animal & {
  honey: boolean
}

const bear = getBear();
bear.name;
bear.honey;
```

```
// 인터페이스에 새 필드 추가하기
interface Window {
  title: string
}

interface Window {
  ts: TypeScriptAPI
}

const src = 'const a = "Hello World"';
window.ts.transpileModule(src, {});

// 타입은 생성된 뒤에 달라질 수 없다.
type Window = {
  title: string
}

type Window = {
  ts: TypeScriptAPI
}
// Error: Duplicate identifier 'Window'.
```
