# Recoil

요즘 채용 공고를 쭈욱 보고 있는데, 생소한 상태 관리 라이브러리가 많이 보여서 정리를 좀 해보고 있다.

처음엔 Context API가 있다는 것만 알고, 뭔지도 모르는 상태에서 Redux를 먼저 시작했었다. (그냥 많이 쓰인다고 해서..)

근데 다른 상태 관리 라이브러리를 보니까 또 Redux가 절대적으로 제일 좋다고 얘기할 순 없겠더라.

만약 Redux가 제일 편하고 제일 좋은 거였다면, 다른 라이브러리가 이렇게 많이 탄생하지도 않았을 뿐더러 사람들이 다 Redux만 썼겠지?

Context API, MobX, Recoil, React-Query, redux의 다양한 미들웨어까지.. 이거 이외에도 아마 더 있을 것 같다.. ㅎ

일단 Context API, React-Query, redux-saga, redux-persist까지는 알아봤으니

오늘은 Recoil이 뭔지, 어떻게 사용하며, 어떤 점 때문에 사용되는지 알아보려고 한다.

<br />

## Recoil

Recoil은 React를 만든 Facebook에서 만든 상태 관리 라이브러리로,

`useState`를 사용하는 것만큼 간단하게 효과적인 상태 관리를 할 수 있도록 도와준다고 한다.

[공식 홈페이지](https://recoils.org)에 따르면, Recoil은 다음과 같은 특징을 갖고 있다고 한다.

- React처럼 작동하며, 앱에 추가할 시 빠르고 유연하게 상태를 공유할 수 있다.

- 파생 데이터와 비동기 쿼리는 순수 함수와 효율적인 구독으로 관리된다.

- 분할된 코드를 손상시키지 않고 앱 전체의 모든 상태 변경을 관찰하여 지속성, 라우팅, 시간 이동 디버깅 또는 실행 취소를 구현한다.

<br />

### 설치

    npm i recoil

    yarn add recoil

리액트 프로젝트에서 위 cli로 Recoil을 설치할 수 있다.

<br />

### RecoilRoot

Recoil state를 사용하는 컴포넌트들은 `<RecoilRoot>`를 필요로 한다.

`<RecoilRoot>`를 사용하기에 가장 적절한 곳은 root 컴포넌트다.

```
// App.js

import React from "react";
import {
  RecoilRoot,
  atom,
  selector,
  useRecoilState,
  useRecoilValue,
} from "recoil";

const App = () => {
  return (
    <RecoilRoot>
      <CharacterCounter />
    </RecoilRoot>
  )
};

export default App;
```

<br />

### Atoms

Recoil에서 아톰은 상태의 단위를 의미하며, 어떤 컴포넌트에서든 쓰여지고 읽혀질 수 있다.

아톰의 value를 읽는 컴포넌트들은 암묵적으로 그 아톰을 구독하고 있기 때문에,

아톰이 업데이트되면 해당 아톰을 구독하는 모든 컴포넌트가 리렌더링된다.

React의 `useState()`와 비슷하지만, **상태가 컴포넌트 간에 공유될 수 있다**는 차이가 있다.

```
const textState = atom({
  key: "textState", // 다른 atoms나 selectors과 구별되기 위한 unique한 id
  default: "", // 기본값(초기값)
});

const CharacterCounter = () => {
  return (
    <div>
      <TextInput />
      <CharacterCount />
    </div>
  );
}

const TextInput = () => {
  const [text, setText] = useRecoilState(textState);

  return (
    <div>
      <input type="text" value={text} onChange={(e) => setText(e.target.value)} />
      <br />
      Echo: {text}
    </div>
  )
}
```

<br />

### Selectors

셀렉터는 atoms나 다른 selectors를 입력으로 받아들이는 순수 함수다.

상위의 atoms 또는 selectors가 업데이트되면 하위의 selector 함수도 다시 실행된다.

즉, 컴포넌트들은 selectors를 atoms처럼 구독할 수 있으며, selectors가 변경되면 해당 selectors를 구독하는 컴포넌트들도 다시 렌더링된다.

최소한의 상태 집합만 atoms에 저장하고, 다른 모든 파생되는 데이터는 selectors에 명시한 함수를 통해 계산함으로써 효율성을 챙길 수 있다.

```
const charCountState = selector({
  key: "charCountState", // 다른 atoms나 selectors과 구별되기 위한 unique한 id
  // 전달되는 get 인자를 통해 atoms와 다른 selectors에 접근할 수 있고,
  // 접근 시 자동으로 종속 관계가 생성되므로 참조했던 atoms나 selectors가 업데이트되면 이 함수도 다시 실행된다.
  get: ({ get }) => {
    const text = get(textState);

    return text.length;
  },
});

const CharacterCount = () => {
  const count = useRecoilValue(charCountState);

  return <>Character Count: {count}</>;
}
```

<hr />

#### `useRecoilState()`

컴포넌트에서 atom을 **읽고 쓰기** 위해 사용하는 훅이다. React의 `useState()`와 비슷하지만, 상태가 컴포넌트 간에 공유될 수 있다는 차이가 있다.

`useRecoilState()`를 컴포넌트에서 사용하면, 상태가 업데이트 되었을 때 리렌더링을 하도록 컴포넌트를 구독한다.

<br />

#### `useRecoilValue()`

atom이나 selectors의 상태를 **조회**할 때 사용하며, 하나의 atom이나 selector를 인자로 받아 대응하는 값을 반환한다.

- 위 예시에서 `charCountState`는 writable하지 않기 때문에 `useRecoilState()`를 사용하지 않는다.

  - `textState`라는 atom에 의존성을 갖는다.

  - `textState`를 입력으로 사용하고, `textState`의 길이를 출력으로 반환하는 순수 함수처럼 동작할 뿐 writable하지 않다.

<br />

#### `useSetRecoilState()`

상태를 변경하기 위해 비동기로 사용될 수 있는 setter 함수를 반환한다.

setter는 새로운 값이나 이전 값을 인수로 받는 updater 함수를 넘겨준다.

`useSetRecoilState()`는 컴포넌트가 상태를 읽지 않고 쓰기만 하려고 할 때 사용하는 것이 좋다.

컴포넌트에서 단순히 setter를 가져오기 위해 `useRecoilState()` 훅을 사용하면, 업데이트를 구독하고 atom 혹은 selector가 업데이트될 때 리렌더링이 발생한다.

`useSetRecoilState()`를 사용하면 컴포넌트를 구독하지 않고도 값을 설정할 수 있다.

<br />

#### `useResetRecoilState()`

주어진 상태를 기본값으로 리셋하는 함수를 반환한다.

`useResetRecoilState()`는 컴포넌트를 구독하지 않고도 상태를 기본값으로 리셋할 수 있게 해준다.

<hr />

### `useRecoilState` vs `useSetRecoilState`

- 공통점

  - 둘 다 상태를 읽고 쓰기 위해 사용할 수 있다.

- 차이점

  - `useRecoilState()`는 컴포넌트를 구독해야 하는 경우에 사용한다.

  - `useSetRecoilState()`는 컴포넌트를 구독하지 않아도 되는 경우에 사용한다.
