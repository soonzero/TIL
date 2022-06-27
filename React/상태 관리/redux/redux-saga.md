# redux-saga

`redux-saga`는 비동기 작업처럼 reducer에서 처리하면 안 되는, 순수 함수가 아닌 작업을 처리하기 위한 리덕스 미들웨어이다.

액션을 모니터링하다가 특정 액션이 발생했을 때 특정 작업을 하는 방식으로 사용하며, 앱의 사이드 이펙트를 보다 쉽게 관리하고 오류를 더 잘 처리하기 위해 사용한다.

1. 비동기 작업을 할 떄 기존 요청을 취소 처리 할 수 있다.

2. 특정 액션이 발생했을 때 다른 액션이 디스패치되게끔 하거나, 자바스크립트 코드를 실행할 수 있다.

3. 웹 소켓을 사용하는 경우 `Channel`이라는 기능을 사용하여 더욱 효율적으로 코드를 관리할 수 있다.

4. API 요청이 실패했을 경우 재요청하는 작업을 할 수 있다.

## Generator 문법

redux-saga를 사용하려면 먼저 Generator 문법을 이해해야 한다.

Generator 문법은

1. 함수를 작성할 때 함수를 특정 구간에 멈춰 놓거나,

2. 원할 때 다시 돌아가게 할 수 있으며,

3. 결과값을 여러 번 반환할 수 있다.

```
function someFunc() {
  return 1;
  return 2;
  return 3;
  return 4;
}
```

일반적으로 함수에서 값을 여러 번에 걸쳐 반환하는 것은 불가능하기 때문에, 위 함수는 호출할 때마다 1을 반환할 것이다.

하지만 제너레이터 함수를 사용하면 함수에서 값을 순차적으로 반환할 수 있으며, 함수의 흐름을 도중에 멈췄다가 나중에 다시 진행할 수도 있다.

### 예시 1

```
function* generatorFunc() {
  console.log("hello");
  yield 1;
  console.log("my name is soonzero");
  yield 2;
  console.log("nice to meet you");
  yield 3;
  return "what's your name?";
}

const generator = generatorFunc();
```

제너레이트 함수를 만들 때에는 `function*`이라는 키워드를 사용하고,

제너레이터 함수를 호출하면 한 객체가 반환되는데 이를 제너레이터라고 부른다.

함수를 호출하면 해당 함수 안의 코드가 바로 시작되지 않고, `generator.next()`를 호출해야 코드가 실행된다.

그리고 하나의 `yield`값을 반환하고 코드의 흐름을 멈춘다.

코드의 흐름을 멈추고 나서 `generator.next()`를 호출하면 이전의 흐름을 그대로 이어 다시 시작된다.

### 예시 2

```
function* calculator() {
  console.log("계산기가 실행되었습니다");
  let a = yield;
  console.log("a값을 입력했습니다");
  let b = yield;
  console.log("b값을 입력했습니다");
  yield a + b;
}

const calc = calculator();
```

```
calc.next(); // "계산기가 실행되었습니다" { value: undefined, done: false }
calc.next(5); // "a값을 입력했습니다" { value: undefined, done: false }
calc.next(10); // "b값을 입력했습니다" { value: 15, done: false }
calc.next(); //  { value: undefined, done: true }
```

## Generator로 액션 모니터링하기

```
function* watchGenerator() {
  console.log("모니터링을 시작합니다");
  while (true) {
    const action = yield;
    if (action.type === "HELLO") {
      console.log("안녕하십니까?");
    }
    if (action.type === "BYE") {
      console.log("안녕히 가십시오");
    }
  }
}

const watch = watchGenerator()
```

```
calc.next(); // "모니터링을 시작합니다" { value: undefined, done: false }
calc.next({ type: "HELLO" }); // "안녕하십니까?" { value: undefined, done: false }
calc.next({ type: "BYE" }); // "안녕히 가십시오" { value: undefined, done: false }
```

redux-saga에서는 이런 원리로 액션을 모니터링하고, 특정 액션이 발생했을 떄 우리가 원하는 자바스크립트 코드를 실행시켜준다.

### while(true)

위 예시에서 본 while(true)문은 무한 루프를 돌게 하는 로직이지만,

제너레이터의 경우 `yield` 이전까지의 작업들만 실행하고 종료하기 때문에 계속 같은 작업을 하더라도 다른 값을 반환할 수 있다.

로그인 로직 같은 경우, 로그인을 시도했을 때 한 번 실행된 후 종료되며 리턴하는 유저 정보는 매번 다르게 받아올 수 있는 것이다.

하지만 직관적이지 않고, 동기적으로 동작한다는 문제가 있다.

### takeEvery, takeLatest

`redux-saga/effects`에는 다양한 유틸함수들이 들어있는데, 그 중에 `takeEvery`와 `takeLatest`가 있다.

`takeEvery`는 특정 액션 타입에 대해 디스패치되는 모든 액션들을 처리하는 것이다.

while(true)문을 사용하는 것과는 달리 비동기적으로 동작한다.

하지만 takeEvery를 사용했을 때, 사용자가 실수 혹은 고의로 여러 번 로그인을 시도한 경우 클릭한 횟수만큼 로그인 시도가 발생했을 것이다.

이 문제를 해결할 수 있는 방법이 바로 가장 많이 사용되는 유틸 함수인 `takeLatest`이다.

이 함수는 연속 클릭을 하더라도 가장 마지막 action만 인지할 수 있도록 해준다.

하지만 동시에 여러 action을 취할 필요가 있는 로직에서는 적절하지 않을 수 있다.

또한 이 함수도 치명적인 단점을 갖고 있는데, 바로 프론트 단에서만 마지막 action을 인지한다는 것이다.

<img src="https://postfiles.pstatic.net/MjAyMjAzMTRfMjM4/MDAxNjQ3MjM0NzgyMTg0.o5GPd6IAtSpB5Erp2NFpPDm_7H-zVR1SMJjveINsYBsg.PqpM5HKSYQf6S2_m_XnKeb-BHshLr1Cr-rLb3l0YDEgg.PNG.kimjihan77/%EC%BA%A1%EC%B2%98.PNG?type=w966" alt="takeLatest" />

예를 들어 SNS에서 게시글을 등록하는 action을 takeLatest 함수를 이용하여 디스패치했다고 가정하자.

이 떄 사용자가 더블 클릭을 하는 바람에 게시글 등록 action이 두 번 실행됐다면 등록 버튼을 클릭했을 당시에는 하나의 게시글만 렌더링 될 것이다.

하지만 실제 서버의 DB에는 두 개의 중복된 게시글이 등록되는 것이므로 새로고침을 하면 같은 게시글이 두 개 보이게 되는 것이다.

그래서 `takeLatest`를 사용할 때에는 서버에서 받아오는 데이터에 대한 점검이 필요하다.
