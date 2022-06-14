# ES6

### const, let

- 자바스크립트에서는 변수를 선언할 때 var를 이용해왔지만, ES6 이후부터는 `const`와 `let`으로 대체되고 있다.

<br />

- `const`

  - 한 번 대입하면 다른 값을 대입할 수 없다.

  - 반드시 선언과 함께 초기화를 해줘야 한다.

    ```
    const a; // error -> 선언과 함께 초기화를 해줘야 함

    const a = 1; // 가능

    a = 0; // error -> 한 번 대입했으므로 다른 값을 대입할 수 없다.
    ```

<br />

- `let`

  - 한 번 대입하더라도 다른 값을 대입할 수 있다.

  - 선언과 초기화가 꼭 동시에 이루어지지 않아도 된다.

    ```
    let b; // 선언

    b = 5; // 초기화
    ```

<br />

- `const`와 `let`은 var과 달리 블록 스코프를 가지기 때문에, 그 스코프 범위 안에서만 접근이 가능하다.

  - 블록 스코프: `if`, `while`, `for`, `function` 등에서 사용하는 { } 내에 속하는 범위

    ```
    if (true) {
      let a = 5;
      const b = 3;
      var c = 2;

      console.log(a); // 5
      console.log(b); // 3
      console.log(c); // 2
    }

    console.log(a); // error
    console.log(b); // error
    console.log(c); // 2
    ```

<hr />

### 객체 리터럴

- 객체에 동적으로 속성을 추가하는 상황에서 코드를 간결하게 작성할 수 있게 되었다.

- 기존 코드

  ```
  var sayNode = function() {
    console.log("Node");
  };

  var es = "ES";
  var oldObject = {
    sayJS: function() {
      console.log("JS");
    },
    sayNode: sayNode,
  };

  oldObject[es + 6] = "Fantastic";
  ```

- 변경 코드

  ```
  let sayNode = () => {
    console.log("Node");
  };

  let es = "ES";
  const newObject = {
    sayJS() {
      console.log("JS");
    },
    sayNode,
    [es + 6]: "Fantastic",
  }
  ```

- 객체의 메소드에 함수를 연결할 때 콜론(`:`)과 function이라는 키워드를 붙이지 않아도 가능하다.

- 또한 sayNode: sayNode 와 같이 중복되는 이름의 변수는 간단히 sayNode 하나만 작성해도 된다.

- 객체의 속성명을 동적으로 생성할 수 있다.

  - 이전에는 객체 리터럴의 바깥에서 [es + 6]으로 만들었지만, 이제 객체 리터럴 안에서 바로 만들 수 있다.

<hr />

### 템플릿 문자열

- 백틱(`)을 이용해서 새로운 문자열을 만들 수 있다.

- 기존에는 변수가 등장할 때마다 따옴표(", ')를 닫고 + 를 통해 연결했으나, 백틱을 이용해서 문자열 안에 변수도 간편하게 넣을 수 있게 되었다.

  ```
  var num1 = 2;
  var num2 = 3;
  var result = num1 + num2;

  var string = num1 + ' + ' + num2 + ' = ' + result;
  console.log(string); // 2 + 3 = 5

  ...

  const newString = `${num1} + ${num2} = ${result}`
  console.log(newString) // 2 + 3 = 5
  ```

<hr />

### 화살표 함수

- 지금도 기존의 function 키워드를 사용해서 함수를 선언할 수 있다.

- ES6 이후로 화살표 함수가 생기면서 많이 사용되고 있다.

  ```
  function add1(a, b) {
    return a + b;
  }

  ...

  const add2 = (a, b) => a + b;
  ```

- 둘 다 같은 기능을 한다. 다만 화살표 함수에서는 function이라는 키워드 대신 => 기호를 사용한다.

  - 이전 코드에 비해 return문을 줄일 수 있다는 장점이 있고, 이 둘은 this 바인딩 방식에서 차이점이 존재한다.

    - forEach()문 안에 function 선언문을 사용했기 때문에 각자 다른 함수 스코프의 this를 갖게 된다.

    - friends의 값을 가져오기 위해 that이라는 변수를 만들어 this값을 미리 저장해놓아야 한다.

    ```
    var relationship = {
      name: "Kim",
      friends: ["Lee", "Choi", "Park"],
      logFriends: function() {
        var that = this; // relationship을 가리키는 this를 that에 저장

        this.friends.forEach(function(friend) {
          console.log(that.name, friend)
        })
      }
    }

    relationship.logFriends();
    ```

    - forEach()문에서 화살표 함수를 사용했기 때문에 바로 바깥의 스코프에 있는 this를 그대로 사용할 수 있다.

    ```
    const newRelationship = {
      name: "Kim",
      friends: ["Lee", "Choi", "Park"],
      logFriends() {
        this.friends.forEach(friend => {
          console.log(this.name, friend);
        })
      }
    }

    newRelationship.logFriends();
    ```
