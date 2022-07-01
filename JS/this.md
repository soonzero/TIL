# this

내가 아직 규모가 큰 프로젝트를 해보지 않아서 그런지, 이전에 프로젝트 진행할 때에는 this를 사용할 일이 전혀 없었다.

this는 면접 질문으로도 자주 출제된다고 해서, 저번에도 공부를 했었는데 다시 보니 기억이 잘 나지 않아서

이번에는 기록을 해보려고 한다.

## this

실행 컨텍스트 내부에는 this를 담당하는 부분인 **ThisBinding**이 있기 때문에,

this의 binding은 해당 실행 컨텍스트가 활성화될 때 세팅되고, 실행 컨텍스트는 함수가 호출될 때 활성화된다.

이는 곧, "this가 함수가 호출될 때에 결정이 된다"라는 의미이고, this의 값은 함수가 호출되는 방법에 의해 결정된다는 것이다.

- ThisBinding: this를 고정하기 위해 사용하는, 값과 연결하는 과정이라고 할 수 있다.

### this의 5가지 경우

#### 1. 전역 공간에서 전역 객체를 가리킨다.

- 브라우저 환경에서의 전역 객체는 **window 객체**를, Node.js 환경에서의 전역 객체는 **global 객체**를 가리킨다.

#### 2. 함수 호출 시

- 함수를 실행한 부분의 공간은 실질적으로 전역 공간이기 때문에 함수 호출시에도 전역 객체를 가리킨다.

#### 3. 메소드 호출 시

- this는 메소드를 호출한 주체(메소드명 앞)를 가리킨다.

#### 4. callback 호출 시

- 함수 호출 시와 동일하게 전역 객체를 가리킨다.

#### 5. 생성자 함수 호출 시

- new 연산자로 생성된 인스턴스를 가리킨다.

<hr />

#### 예제1

```
const someone = {
  name: "soonzero",
  whoAmI () {
    console.log(this)
  }
}

someone.whoAmI(); // someone을 주체로 whoAmI 함수를 실행했으므로 someone 객체를 출력한다.

const myWhoAmI = someone.whoAmI;
myWhoAmI(); // 전역 공간에서 whoAmI를 실행했으므로 전역 객체를 출력한다.

// this는 this를 호출한 객체를 가리키기 때문에, this를 호출한 객체에 따라 다르다.


// bind를 사용해서 주체를 지정해 줄 수 있다.
// 호출하는 주체와 무관하게 주체를 고정할 때 사용한다.
const bindedMyWhoAmI = myWhoAmI.bind(someone)
```

#### 예제2

```
const rs1 = {
  name: "soonzero",
  friends: ["soonone", "soontwo", "soonthree"],
  printFriends() {
    this.friends.forEach(
      function (friend) {
        console.log(this.name, friend)
      }
    )
  },
};

rs1.printFriends();
// undefined soonone
// undefined soontwo
// undefined soonthree
```

- this.name이 undefined로 출력되는데, 무명 함수(파라미터 friend를 인자로 받는) 스코프의 this는 전역 객체를 가리킨다.

- 따라서 name이라는 멤버가 없기 떄문에 값을 줄 수 없어 undefined가 출력된다.

```
const rs2 = {
  ...
  printFriends() {
    const that = this;
    this.friends.forEach(function (friend) {
      console.log(that.name, friend)
    })
  }
}

rs2.printFriends();
// soonzero soonone
// soonzero soontwo
// soonzero soonthree

// 객체 rs2을 가리키는 this를 that에 저장해서 사용하면 rs2의 name이 정상적으로 출력된다.
```

```
const rs3 = {
  name: "soonzero",
  friends: ["soonone", "soontwo", "soonthree"],
  printFriends() {
    // 화살표 함수를 사용하면 printFriends() 안의 this를 그대로 사용할 수 있다.
    // 상위 스코프의 this를 그대로 물려받기 때문이다.
    this.friends.forEach(friend => {
      console.log(this.name, friend);
    })
  }
}
```

- 일반 함수의 this는 전역 객체를 가리키고, 화살표 함수의 this는 상위 스코프의 this를 가리킨다.

- 화살표 함수를 사용해서 함수의 상위 스코프인 rs3의 this를 물려받아 사용할 수 있다.

- 이 방법이 가장 권장되는 방법이다.

### call, apply, bind

- call: this를 바인딩하면서 함수를 호출하는 것으로, 인자를 하나씩 넘긴다.

```
const type = "zero";
const type1 = { type: "one" };
const type2 = { type: "two" };

function getType() {
  console.log(this.type)
}

getType(); // zero (전역 객체의 type)
getType.call(this); // zero (전역 객체의 type)
getType.call(type1); // one (type1.type)
getType.call(type2); // two (type2.type)
```

- apply: call과 같이 this를 바인딩하면서 함수를 호출하지만, 인자를 배열로 한 번에 넘긴다.

```
const obj1 = { val1: 1, val2: 2, val3: 3 };
const obj2 = { v1: 10, v2: 50, v3: 100, v4: 25 };

function sum() {
  const _sum = 0;
  for (name in this) {
    _sum += this[name]
  }
  return _sum;
}

alert(sum.apply(obj1)) // 6
alert(sum.apply(obj2)) // 185
```

- bind: 함수를 호출하는 것이 아니고, this가 바인딩된 새로운 함수를 반환한다.

<hr />

### strict 모드에서의 this

`use strict`는 strict mode(엄격 모드)의 선언 방식으로, 안전한 코딩을 위한 하나의 가이드라인이다.

- 몇 가지 액션들을 실행할 수 없도록 하고, 좀 더 많은 예외를 발생시킨다.

- 코드의 오류 검사를 더 나은 방법으로 적용할 수 있다.

- 암시적으로 선언한 변수를 사용하거나, 읽기 전용 속성에 값을 할당하거나, 확장할 수 없는 개체에 속성을 추가할 수 없다.

**strict mode에서, 일반 함수에서의 this는 undefined가 바인딩된다.**
