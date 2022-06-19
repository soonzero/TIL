# TypeScript

## TypeScript

TypeScript는 동적타입 언어인 바닐라 JS에서 \*정적 타이핑을 지원하는 언어이다.

- 동적 타입(Dynamic Type): 런타임 시 Type이 결정되는 방식

- 정적 타이핑(Static Typing): 변수에 타입을 미리에 선언하고, 컴파일할 때 체크해주는 방식

이러한 점에서 에러를 잡을 수 있고, TypeScript로 작성하더라도 JavaScript로 컴파일해주기 때문에 babel을 사용할 필요도 없으며 다양한 브라우저에서 사용 가능하다.

한 마디로 정리하면, 내가 원할 때 정적 타이핑을 할 수 있는 자바스크립트

<hr />

## TypeScript를 사용하는 이유

### Type 안정성과 더 나은 개발 디버깅

```
message.toLowerCase();

message();
```

자바스크립트는 위 코드의 첫 번째 줄을 `toLowerCase`라는 프로퍼티에 접근한 이후에 실행하고,

두 번째 줄은 `message`를 바로 호출할 것이다.

<br />

**하지만, `message`가 어떤 value를 가지고 있는지 모른다고 가정해보자.**

- `message`는 호출이 가능한가?

- `toLowerCase`라는 프로퍼티를 갖고 있는가?

- 만약에 갖고 있다면, `toLowerCase`는 호출이 가능한가?

- 두 가지를 모두 호출할 수 있다면 각각 어떤 것을 return할까?

자바스크립트를 사용할 때에는 위 질문에 대한 대답을 머리 속에 갖고 있고, 우리는 항상 모든 게 에러 없이 실행되기를 바랄 뿐이다.

<br />

```
const message = "Hello World!";
```

그렇다면 만약 `message`를 위와 같이 정의한다면 어떨까?

- `message.toLowerCase()`를 실행하면, 소문자로 변경된 문자열을 return할 것이고,

  ```
  TypeError: message is not a function
  ```

- `message`를 호출하면, 위의 에러가 발생할 것이다.

<br />

자바스크립트 런타임은 value의 `type`을 유추하고, 어떤 behaviors와 capabilities를 갖고 있는 지 확인한다.

그렇기 때문에 `"Hello World!"`가 function으로써 호출될 수 없다는 `TypeError`가 발생하는 것이다.

<br />

TypeScript를 사용하면, 코드 작성 시 데이터를 어떤 Type으로 지정할 것인지 미리 작성할 수 있기 때문에,

프로그램 실행 전에 미리 에러를 확인할 수 있고 데이터가 어떤 Type으로 지정되는지 직관적으로 알 수 있다.

또한, 만약 함수가 어떤 타입의 파라미터를 수용하는지 모를 경우 함수의 코드를 깊게 분석하지 않고도 빠른 디버깅이 가능하다.

<hr />

### 자바스크립트와 상호 호환

위에서 언급했듯, TypesScript를 사용하면서 JavaScript도 자유롭게 사용 가능하며 JS 라이브러리도 사용할 수 있다.

이 말은 곧 JS를 사용하는 프로젝트라면 TS도 사용할 수 있다는 것이고 프론트엔드 프레임워크나 백엔드 node.js에서도 사용이 가능하다는 것이다.

<hr />

### 코드의 품질

데이터 구조를 types와 interface를 사용하여 정의해두면, 처음부터 데이터 구조에 대한 더 나은 설계가 가능하다.
