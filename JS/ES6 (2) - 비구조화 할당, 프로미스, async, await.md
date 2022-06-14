# ES6

### 비구조화 할당

- 객체나 배열에서 속성 혹은 요소를 꺼내올 때 사용한다.

  ```
  var fridge = {
    status: {
      name: "milk",
      count: 3,
    },
    getMilk: function() {
      return "thanks!";
    }
  };

  var getMilk = fridge.getMilk;
  var count = fridge.status.count;
  ```

- 기존에는 객체에서 속성을 가져올 때 위와 같이 작성했다면, ES6부터는 아래와 같이 간결하게 작성할 수 있게 되었다.

  ```
  const refrigerator = {
    status: {
      name: "milk",
      count: 3,
    },
    getMilk() {
      return "thanks!";
    }
  };

  const { getMilk, status: { count } } = refrigerator;

  console.log(getMilk()) // thanks!
  console.log(count) // 3
  ```

- 배열도 마찬가지로 적용이 가능하다.

  ```
  var array = ["es6", {}, 5, false];

  var js = array[0];
  var obj = array[1];
  var bool = array[array.length - 1];
  ```

- 이 세 줄의 코드를 단 한 줄로 표현할 수 있다.

  ```
  const newArray = ["es6", {}, 5, false];

  const [js, obj, , bool] = newArray;
  ```

- 비구조화 할당을 이용하면, 배열이 위치마다 변수를 넣어 똑같은 역할을 하도록 만들 수 있다.

<hr />

### 프로미스(Promise)

- 자바스크립트는 비동기 프로그래밍으로 콜백 함수를 많이 사용하게 되는데, 콜백 함수를 많이 사용하게 되면 복잡해질 수 있다.

- ES6부터는 콜백 대신 API들이 프로미스 기반으로 재구성되고 있기 때문에, 프로미스를 잘 이용하면 복잡한 콜백 함수들에서 벗어날 수 있다.

  ```
  const condition = true;

  const promise = new Promise((resolve, reject) => {
    if (condition) {
      resolve("성공");
    } else {
      reject("실패");
    }
  });

  promise
    .then(message => {
      console.log(message)
    })
    .catch(error => {
      console.log(error)
    });
  ```

- new Promise로 새롭게 생성한 프로미스에 `resolve`와 `reject`를 매개변수로 갖는 콜백 함수를 넣는다.

- 그리고 선언한 promise 변수에 `then`과 `catch` 메소드를 붙일 수 있게 된다.

- `resolve`가 호출되면 then이 실행되고, `reject`이 호출되면 catch가 실행된다.

- `resolve`와 `reject`에 넣어준 인자는 각각 `then`과 `catch`의 매개변수에서 받을 수 있게 된다.

- `condition`이 `true`이기 때문에 위 코드에서는 `성공`이라는 텍스트가 message가 되어 콘솔에 출력된다.

<br />

- 다음은 예제이다.

  ```
  function findAndSaveUser(Users) {
    Users.findOne({ }, (err, user) => { // 콜백 1
      if (err) => {
        return console.error(err);
      }
      user.name = "kim";
      user.save((err) => { // 콜백 2
        if (err) {
          return console.error(err);
        }
        Users.findOne({ gender: "m" }, (err, user) => { // 콜백 3
          // 생략
        })
      })
    })
  }
  ```

- 위 코드에서는 콜백 함수가 세 번 중첩된 것을 확인할 수 있고, 각각의 콜백 함수에서 에러도 따로 처리해주고 있다.

- 하지만 프로미스를 활용하면 아래와 같이 코드를 작성할 수 있다.

  ```
  function newFindAndSaveUser(Users) {
    Users.findOne({ })
      .then(user => {
        user.name = "kim";
        return user.save();
      })
      .then(user => {
        return Users.findOne({ gender: "m"});
      })
      .then(user => {
        // 생략
      })
      .catch(err => {
        console.error(err);
      });
  }
  ```

- 이전 코드와 비교하면 다음이 다르다.

  - `then`을 사용해서 코드가 깊어지지 않았다.

  - `catch` 구문을 한 번에 작성하고 처리했다.

- `Promise.all`을 사용하면, 프로미스 여러 개를 한 번에 실행할 수 있다.

  ```
  const promise1 = Promise.resolve("성공1");
  const promise2 = Promise.resolve("성공2");

  Promise.all([promise1, promise2])
    .then(result => {
      console.log(result);
    })
    .catch(error => {
      console.error(error);
    });
  ```

- `Promise.all`에 해당하는 모든 프로미스가 `resolve` 상태여야 `then` 구문으로 넘어가며, 여러 개의 프로미스 중 하나라도 `reject`된다면 `catch` 구문으로 넘어간다.

- 기존의 콜백 함수로는 여러 번 중첩해야 구현 가능했지만, 프로미스를 사용하면 비교적 깔끔하게 정리할 수 있다.

<hr />

### async / await

- `async`와 `await`은 ES2017에 추가된 기능이지만, 비동기 프로그래밍을 할 때 유용하게 사용되고, 위에서 알아봤던 프로미스를 더욱 깔끔하게 만들어준다.

- 다음 코드는 콜백의 깊이 문제를 해결해서 비교적 깔끔해졌지만, 아직 코드의 길이가 길다.

  ```
  function newFindAndSaveUser(Users) {
    Users.findOne({ })
      .then(user => {
        user.name = "kim";
        return user.save();
      })
      .then(user => {
        return Users.findOne({ gender: "m"});
      })
      .then(user => {
        // 생략
      })
      .catch(err => {
        console.error(err);
      });
  }
  ```

- 이 코드에 `async`와 `await` 문법을 사용하면 다음과 같이 더 깔끔하게 다듬어줄 수 있다.

  ```
  async function findAndSaveUser(Users) {
    try {
      let user = await Users.findOne({ });
      user.name = "kim";
      user = await user.save();
      user = await Users.findOne({ gender: "m" });

      // 생략
    } catch (e) {
      console.error(e);
    }
  }
  ```

- function 앞에 `async`를 붙여주고, 프로미스 앞에는 `await`을 붙여주면 되는데, `await`을 붙인 프로미스가 `resolve` 상태가 될 때까지 기다린 후 다음 구문으로 넘어가는 방식이다.

- 화살표 함수를 이용하면 다음과 같다.

  ```
  const findAndSaveUser = async (Users) => {
    try {
      let user = await Users.findOne({ });
      user.name = "kim";
      user = await user.save();
      user = await Users.findOne({ gender: "m" });

      // 생략
    } catch (e) {
      console.error(e);
    }
  }
  ```

- 화살표 함수를 사용하면서도 `async`/`await` 문법으로 간단히 코드를 작성할 수 있다.
