# Hoisting과 TDZ

"호이스팅"은 자바스크립트를 배운 사람이라면 누구나 한 번쯤 들어봤을 단어이다.

나도 자바스크립트를 처음 독학할 때부터 유튜브 영상이든, 인터넷 강의든 관계없이 "호이스팅"이라는 단어를 굉장히 많이 접했다.

그래서 이 단어를 보면 뭔지는 대-충 안다. 하지만 설명하라고 하면.. 어디서부터 어떻게 설명해야할지 감이 잘 잡히지가 않는다.

생각난 김에 확실히 설명할 수 있을 정도로 정리하고, 이와 관련된 TDZ도 같이 공부해보려고 한다.

## `var`

`var` 키워드는 ES5까지 변수를 선언할 수 있는 키워드로 사용되었으며 특징은 다음과 같다.

1. 변수 중복 선언 가능

   ```
   var name = "soonzero";
   var name = "soonone";
   console.log(name) // soonone
   ```

2. `hoisting`

   - `hoist`는 '끌어올리기'라는 뜻을 가지고 있는 단어이며, 스코프 안에 있는 선언들을 모두 스코프의 최상단으로 끌어올리는 것을 의미한다.

   - 변수가 함수 내에서 정의되었을 경우에는 선언이 함수의 최상위로, 함수의 바깥에서 정의되었을 경우에는 전역 컨텍스트의 최상위로 변경이 된다.

     ```
     console.log(name); // undefined
     var name = "soonzero";
     ```

     - 위 코드를 실제 발생하는 것처럼 재구성하면 아래와 같다.

       ```
       var name; // 선언문을 제일 위로 끌어올린다.

       console.log(name); // undefined
       name = "soonzero"
       ```

3. 함수 레벨 스코프

   ```
   (function () {
     var local = 1;
   })();
   console.log(local); // Uncaught ReferenceError: local is not defined

   for (var i = 0; i < 10; i++) {}
   console.log(i) // 10
   ```

   - 위 예시처럼 함수 스코프만 인정되기 때문에 for문 내에서 선언한 변수 i도 외부에서 참조가 된다.

4. `var` 키워드 생략 가능

   ```
   var globalVariable = "global!";

   if (globalVariable === "global!") {
     globlVariable = "global?" // globalVariable => globlVariable로 오타 발생
   }

   console.log(globalVariable); // global!
   console.log(globlVariable); // global?
   ```

   - `globalVariable` 변수를 `globlVariable`로 오타를 냈지만, var 키워드를 작성하지 않았음에도 불구하고 `globlVariable`이 변수로 선언되었다.

<hr />

## `let`과 `const`

`var` 키워드의 경우, 전역 변수를 남발하기 쉽고, 로컬 변수라고 해도 변수의 스코프가 굉장히 넓다.

따라서 변수의 선언부와 호출부가 너무 멀리 떨어져 있거나 값이 의도하지 않게 바뀌는 경우를 추적하기 힘들다.

이러한 문제점 때문에 ES6부터 새로운 변수 키워드로 `let`과 `const`가 등장하게 되었다.

`var`과 똑같은 예시를 들어보면, `var`과 달리 `let`과 `const`는 변수를 재선언할 수 없다는 것을 알 수 있다.

```
let name = "soonzero";
let name = "soonone"; // Uncaught SyntaxEror: Identifier 'name' has already been declared

const name = "soonzero";
const name = "soonone"; // Uncaught SyntaxEror: Identifier 'name' has already been declared
```

그렇다면 `hoisting`은 발생할까?

```
console.log(name); // undefined
var name = "soonzero";

console.log(aaa) // Uncaught ReferenceError: aaa is not defined

console.log(name); Uncaught ReferenceError: Cannot access 'name' before initialization
let name = "soonzero";
```

- `var` 키워드를 사용하여 선언한 변수를 호출하면, 참조 에러가 발생하는 것이 아닌 `undefined`가 출력된다.

- 선언조차 하지 않은 변수를 호출하게 되면, `Uncaught ReferenceError: aaa is not defined` 에러 메세지가 출력된다.

- `let` 키워드를 사용해 선언한 변수를 호출하면, 두 번째와 마찬가지로 참조 에러가 발생하지만 다른 에러 메세지를 볼 수 있다.

  - `Uncaught ReferenceError: Cannot access 'name' before initialization`

## 변수 생성 과정과 TDZ

JS 인터프리터 내부에서 변수는 총 3단계에 걸쳐 생성된다.

1. 선언: 스코프와 변수 객체가 생성되고, 스코프가 변수 객체를 참조한다.

2. 초기화: 변수 객체가 가질 값을 위해 **메모리에 공간을 할당**한다. 이 때 초기화되는 값은 `undefined`이다.

3. 할당: 변수 객체에 값을 할당한다.

<br />

`var` 키워드는 선언과 초기화가 동시에 이루어지기 때문에, 선언하자마자 undefined로 값이 초기화된다. 즉, 호이스팅 시 undefined로 변수를 초기화한다.

하지만 `let`과 `const`는 선언은 되어있으나 **아직 초기화가 이루어지지 않아 변수에 담길 값을 위한 공간이 메모리에 할당되지 않은 상태**로 남아있고,

이 상태로 남아 있는 구간을 `TDZ(Temporal Dead Zone)`이라고 한다.

즉, `TDZ` 구간에서 해당 변수에 접근을 시도하면, 위에서 볼 수 있던 `Cannot access ___ before initialization` 에러 메세지를 만나는 것이다.

## 결론

정리하자면, `hoisting`이란 변수 선언문의 위치와 관계 없이 변수 선언문을 가장 먼저 실행하는 것이라고 말할 수 있다.

`var`, `let`, `const` 모두 호이스팅은 발생한다.

하지만 `let`과 `const`는 `var`과 달리 `TDZ` 라는 영역을 사용해서 참조를 방어하기 때문에 참조 에러가 발생하는 것이다.

반드시 ES5로 코드를 작성해야 하는 경우를 제외한다면 `var` 키워드는 사용을 최대한 지양하는 것이 좋다.
