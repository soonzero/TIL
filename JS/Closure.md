# Closure (클로저)

클로저는 두 개의 함수로 만들어진 `환경`으로 이루어진 특별한 객체의 한 종류이다.

`환경`이란 클로저가 생성될 때 그 범위에 있던 여러 지역 변수들이 포함된 `context`를 말한다.

이 클로저를 통해 JS에는 없는 비공개(private) 속성/메소드, 공개 속성/메소드를 구현할 수 있는 방안을 마련할 수 있다.

<br />

## 클로저 생성하기

클로저가 생성되는 조건은 다음과 같다.

1. 내부 함수가 익명 함수로 되어 외부 함수의 반환값으로 사용된다.

2. 내부 함수는 외부 함수의 실행 환경(execution environment)에서 실행된다.

3. 내부 함수에서 사용되는 변수 `x`는 외부 함수의 변수 범위에 있다.

```
function outerFunc() {
  let greeting = "hello";

  function innerFunc() {
    console.log(name);
  })
  innerFunc();
}
outerFunc();

// 출력: "closure"
```

`outerFunc` 함수를 실행시키는 `context`에는 `greeting`이라는 변수가 존재하지 않는다는 것을 확인할 수 있다.

비슷한 맥락에서 다음과 같이 코드를 변경해보자.

```
let say = "hi";

function outerFunc() {
  let say = "hey";

  return function innerFunc() {
    console.log(say);
  };
}

let callFunc = outerFunc();
callFunc();

// 출력: "hey"
```

위 코드에서 `callFunc`를 클로저라고 한다. `callFunc` 호출에 의해 `say`라는 값이 콘솔에 출력되는데, 찍히는 값은 `hi`가 아니라 `hey`라는 값이다.

즉, `outerFunc` 함수의 context에 속해 있는 변수를 참조하는 것이다.

여기서 `outerFunc` 함수의 지역 변수로 존재하는 `name` 변수를 `free variable(자유 변수)`라고 한다.

<br />

다른 프로그래밍 언어에서, 함수의 지역 변수는 함수가 실행되는 동안에만 존재한다.

`outerFunc`이 호출되고 끝난 다음에 더 이상 `name` 변수에는 접근하지 못해야 할 것 같지만, JS에서는 그렇지 않다.

JS의 함수가 클로저를 형성하기 때문이다. 위의 경우에, `callFunc`은 `outerFunc`이 실행될 때 만들어진 `innerFunc`의 인스턴스를 참조한다.

`innerFunc`의 인스턴스는 `name` 변수를 가진 Lexical Environment를 계속해서 유지하기 때문에,

`callFunc`이 실행될 때에도 `name` 변수는 사용 가능한 상태로 남아있는 것이다.

<hr />

**이처럼 외부 함수 호출이 종료되더라도 외부 함수의 지역 변수 및 변수 스코프 객체의 체인 관계를 유지할 수 있는 구조를 클로저라고 한다.**

**보다 정확히는 외부 함수에 의해 반환되는 내부 함수를 가리키는 말이다**
