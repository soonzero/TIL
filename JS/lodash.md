# lodash

lodash는 자바스크립트의 외부 라이브러리 중 하나로, 객체 타입의 깊은 복사를 하기 위해 주로 많이 사용된다고 한다.

이외에도 다양한 기능이 있으니 더 자세한 정보가 필요하면 [lodash의 공식 문서](https://lodash.com/docs/4.17.15)를 확인하는 것이 좋겠다.

## 설치

```
npm i lodash
```

## \_cloneDeep() - 객체 타입의 깊은 복사

```
import _ from "lodash";

const user = {
  name: "soonzero",
  age: 35,
  emails: ["soonzero@gmail.com"],
}

const copiedUser = _cloneDeep(user); // 재귀적으로 복제하여 객체 내부의 모든 프로퍼티를 다른 메모리 주소로 복제한다.
console.log(copiedUser === user); // false
```

## \_uniq(\[array\])

배열에서 중복되는 항목을 제거하고 새로운 배열을 만들어 반환한다.

```
import _ from "lodash";

const usersOfA = [
  { userId: "1", name: "soonzero" },
  { userId: "2", name: "soonone" }
];

const usersOfB = [
  { userId: "1", name: "soonzero" },
  { userId: "3", name: "soontwo" }
];

const usersOfC = usersA.concat(usersOfB);
console.log(usersOfC);
// [
//   { userId: "1", name: "soonzero" },
//   { userId: "2", name: "soonone" },
//   { userId: "1", name: "soonzero" },
//   { userId: "3", name: "soontwo" }
// ]
```

이렇게 단순히 `concat()` 메소드를 이용하는 경우 중복되는 인덱스 값이 생길 수 있다.

이를 제거해 줄 수 있는 lodash 메소드 중 대표적인 것이 바로 `uniqBy()` 메소드다.

### \_.uniqBy()

하나의 배열 데이터에서 고유한 속성을 기준으로 중복이 발생한 데이터를 제거하고 새로운 배열을 반환하는 메소드다.

```
_.uniqBy(array, 중복을 구분할 고유 속성)
```

```
console.log(_.uniqBy(usersOfC, "userId"))
// [
//   { userId: "1", name: "soonzero" },
//   { userId: "2", name: "soonone" },
//   { userId: "3", name: "soontwo" }
// ]
```

위와 같이 `concat()` 메소드들 이용해 생성된 usersOfC 배열 데이터에서 중복된 데이터가 삭제된 것을 볼 수 있다.

이와 비슷한 메소드로는 \_unionBy(array1, arry2, 중복을 구분할 고유 속성)이 있다.

### \_.unionBy()

여러 개의 배열 데이터를 고유한 속성을 기준으로 중복을 제거하고, 새로운 배열 데이터로 반환한다.

```
_.unionBy(array1, array2, 중복을 구분할 고유 속성)
```

`_.uniqBy()` 메소드와 다른 점이 있다면, `_.unionBy()`는 합치고자 하는 데이터를 이용해서

중복이 생기지 않도록 새로운 배열을 만들어서 반환해준다는 점이다.

```
const usersOfD = _unionBy(usersOfA, usersOfB, "userId")
console.log(usersOfD)
// [
//   { userId: "1", name: "soonzero" },
//   { userId: "2", name: "soonone" },
//   { userId: "3", name: "soontwo" }
// ]
```

### _.find(), _.findIndex()

데이터의 양이 많은 객체나 배열에서 원하는 값이나 인덱스를 빠르게 찾을 수 있는 메소드이다.

```
_.find(collection, 찾을 조건)
_.findIndex(collection, 찾을 조건)
```

```
import _ from "lodash";

const users = [
  { userId: "1", name: "soonone" },
  { userId: "2", name: "soontwo" },
  { userId: "3", name: "soonthree" },
  { userId: "4", name: "sooonfour" },
  { userId: "5", name: "soonfive" },
  { userId: "6", name: "soonsix" },
  { userId: "7", name: "soonseven" }
]

const foundUser = _.find(users, { name: "soonsix" });
const foundIndex = _.findIndex(users, { name: "soonthree" })

console.log(foundUser);
// { userId: "6", name: "soonsix" }

console.log(foundIndex);
// 2
```

### \_.remove()

원하는 조건에 해당되는 값을 기존 array에서 제거하고 배열로 반환하는 메소드이다.

```
_.remove(collection, 제거할 데이터)
```

```
_.remove(users, { name: "soonfour" });
console.log(users)
[
  { userId: "1", name: "soonone" },
  { userId: "2", name: "soontwo" },
  { userId: "3", name: "soonthree" },
  { userId: "5", name: "soonfive" },
  { userId: "6", name: "soonsix" },
  { userId: "7", name: "soonseven" }
]
```
