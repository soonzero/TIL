# useState의 비동기 처리와 함수형 업데이트

## useState의 비동기적 속성과 batching

### useState의 비동기적 속성

리액트에서 `setState`를 사용해서 상태를 업데이트할 경우, 업데이트된 상태는 즉시 반영되지 않는다.

`setState`함수는 비동기적으로 작동하기 때문이다.

즉, 리렌더링이 되고 나서야 비로소 `state`는 업데이트된 값을 가지게 된다.

간단히 순서를 설명하면 다음과 같다.

1. `setState`로 state를 변경하려고 버튼을 클릭

2. state의 변경을 감지하고 리렌더링 (이전까지는 변경을 감지했지만 실제로 state는 이전 값을 가짐)

3. 리렌더링 완료 후 state를 최신 값으로 반영

```
import { useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  const countUpAtOnce = () => {
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
  };

  const countDownAtOnce = () => {
    setCount(count - 1);
    setCount(count - 1);
    setCount(count - 1);
  };

  return (
    <div className="App">
      <div>{count}</div>
      <button onClick={countUpAtOnce}>+3</button>
      <button onClick={countDownAtOnce}>-3</button>
    </div>
  );
}

```

state는 리렌더링이 발생하기 전까지는 최신값으로 업데이트되지 않는다.

따라서 `countUpAtOnce`와 `countDownAtOnce`는 각 함수 내 마지막 라인만 인식하여 +1과 -1의 결과만 반영된다.

이처럼 여러 state를 동시에 업데이트하는 경우, 리액트는 state를 batching하여 업데이트를 진행한다.

### batching

배칭이란 전달된 오브젝트들을 하나로 합치는 작업을 의미한다.

아래 코드는 실제로 리액트의 batching이 발생하는 예제를 간단하게 나타낸 것이다.

```
const [value, setValue] = useState(0)

setValue(value + 1);
setValue(value + 1);
setValue(value + 1);

// Expected value: 3
// Result value: 1
```

이러한 특성 때문에 업데이트된 state를 즉시 반영해야 할 때 `useEffect`를 자주 사용한다.

## 함수형 업데이트

하지만 꼭 `useEffect`를 사용해야 하는 것은 아니다.

문제를 해결하기 위한 다른 방법은 **함수형 업데이트**로, `setState`에 값을 그대로 전달하는 것이 아니라 값을 변경할 수 있는 함수를 전달하는 것이다.

```
const [value, setValue] = useState(0)

setValue(value => value + 1); // 1
setValue(value => value + 1); // 2
setValue(value => value + 1); // 3

// Expected value: 3
// Result value: 3
```

이렇게 함수형 업데이트를 사용하면 상태를 바로 업데이트할 수 있기 때문에, 위 사례처럼 이전 상태를 이용하여 상태를 업데이트하는 경우에는

함수형 업데이트를 사용하는 것이 더 효율적이다.

위에서 사용했던 예제를 다음과 같이 적용해볼 수 있다.

```
import { useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  const countUpAtOnce = () => {
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
  };

  const countUpOneByOne = () => {
    setCount((count) => count + 1);
    setCount((count) => count + 1);
    setCount((count) => count + 1);
  };

  const countDownAtOnce = () => {
    setCount(count - 1);
    setCount(count - 1);
    setCount(count - 1);
  };

  const countDownOneByOne = () => {
    setCount((count) => count - 1);
    setCount((count) => count - 1);
    setCount((count) => count - 1);
  };

  return (
    <div className="App">
      <div>{count}</div>
      <button onClick={countUpAtOnce}>+3</button>
      <button onClick={countUpOneByOne}>+1+1+1</button>
      <button onClick={countDownAtOnce}>-3</button>
      <button onClick={countDownOneByOne}>-1-1-1</button>
    </div>
  );
}
```

또한 함수형 업데이트는 `useCallback`과 함께 props로 전달된 함수를 최적화할 때에도 유용하게 사용될 수 있다.

예를 들어 부모 컴포넌트에 존재하는 `handleClick` 함수를 자식 컴포넌트에게 props를 통해 전달할 경우,

`useCallback`을 사용해서 함수를 감싸 최적화할 수 있다.

```
const handleToggle = useCallback(() => setIsClicked(!isClicked), [isClicked])
```

하지만 이런 경우에는 `useCallback`과 함께 사용하더라도 `isClicked`에 대한 의존성 배열을 갖게 되기 때문에, 최적화를 하는 의미가 없어지게 된다.

```
const handleToggle = useCallback(() => setIsClicked(prev => !prev), [])
```

이렇게 함수형 업데이트를 사용해서 최신값을 사용하면서도 최적화도 할 수 있다.
