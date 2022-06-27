# Context API와 Redux

## 전역 상태 관리

상태(state)는 컴포넌트에서 사용되는 변경 가능한 데이터를 의미한다.

- props와의 차이점

  - props: 컴포넌트 간 전달되는 데이터

  - 상태: 컴포넌트 내에서 관리되고 시간이 지남에 따라 변경이 되는 동적인 데이터

리액트의 상태는 '범위'에 따라 `Local State`와 `Global State`로 나눌 수 있다.

- `Local State`: 몇몇 컴포넌트에만 영향이 있음

  - `useState`를 통해 특정 컴포넌트에 한정되어 관리되거나 props로 전달 가능

- `Global State`는 여러 개의 혹은 전체 컴포넌트에 영향을 끼침

  - `useState`로 관리한다면 props를 여러 개의 컴포넌트에 전달해야 한다는 문제점이 생김.

  - 부모 컴포넌트 App에서 UI 테마를 변경할 경우

    - 자식 컴포넌트 Component1에 변경된 UI 테마 props를 전달

    - 자식 컴포넌트 Component2에 변경된 UI 테마 props를 전달

    - 자식 컴포넌트 Component3에 변경된 UI 테마 props를 전달

    - ...

즉, 부모 컴포넌트에서 변경된 특정 props를 자식 컴포넌트로 내려주는 과정을 여러 번 진행해야 한다.

앱의 규모가 작다면 크게 상관은 없겠으나, 규모가 커질수록 props 전달 과정이 복잡해지기 때문에 상태 관리가 어려워질 수 있다.

<img src="https://user-images.githubusercontent.com/95613159/175510023-24be8f74-1347-44bd-9ebe-6a195bd9c57b.png" alt="prop drilling" />

이러한 문제를 prop drilling이라고 하는데, 컴포넌트 간에 일일이 props를 전달하는 것을 의미한다.

Context API와 Redux는 이런 prop drilling 문제와 컴포넌트 간의 의존성 문제를 해결하기 위해 사용된다.

### Context API

<img src="https://ichi.pro/assets/images/max/724/1*mOpxRXP7O0laXUDIqzm2_Q.png" alt="context api" />

Context API는 리액트에서만 사용되는 전역 상태 관리를 위한 내장 라이브러리이다.

- `Context`: 리액트 컴포넌트 트리 내에서 전역적으로 데이터를 관리할 수 있도록 고안된 방법.

  - `useState`와 `useReducer`로 상태 관리가 이루어지고, `Context`는 이를 위한 수단으로 사용된다.

- `createContext()`: context 객체를 생성한다.

- `Provider`: context를 구독하는 컴포넌트들에게 context의 변화를 알린다.

  - Provider 컴포넌트는 부모 컴포넌트에서 자식 컴포넌트로 전역 데이터를 전달할 때 사용한다.

- `Consumer`: context의 변화를 구독하는 컴포넌트

- `useConsumer`: 해당 컴포넌트의 가장 가까이에 있는 Provider의 value를 반환한다.

#### 사용 방법

```
const themes = {
  light: {
    foreground: "#000000",
    background: "#eeeeee",
  },
  dark: {
    foreground: "#ffffff",
    background: "#222222",
  },
};

// 전역 변수 themes에 대한 context 객체를 선언
const ThemeContext = React.createContext(themes.light);

// Provider를 활용하여 themeContext를 구독하는 컴포넌트들에게 context의 변화를 알리도록 설정
const App = () => {
  return (
    <ThemeContext.Provider value={themes.dark}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

const Toolbar = (props) => {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

const ThemedButton = () => {
  // useContext를 활용하여 themeContext에서 전달하고 있는 현재 값을 반환
  const theme = useContext(ThemeContext);
  return (
    <button style={{ background: theme.background, color: theme.foreground }}>
      This is styled ThemeContext
    </button>
  );
}
```

### Redux

<img src="https://d33wubrfki0l68.cloudfront.net/01cc198232551a7e180f4e9e327b5ab22d9d14e7/b33f4/assets/images/reduxdataflowdiagram-49fa8c3968371d9ef6f2a1486bd40a26.gif" alt="redux" />

Redux는 React, Vue, Angular 등 다양한 환경에서 사용 가능한 전역 상태 관리 라이브러리이다.

- `Reducer`: action을 받아서 새로운 state를 반환하는 순수 함수로, action을 통해 앱의 state를 어떻게 업데이트하는지 작성한다.

- `store`: 전역 state를 관리하는 객체로 아래의 메소드를 가지고 있으며, 리덕스는 한 개의 store만 정의 가능하다.

  - 사전에 정의한 reducer를 활용하여 `createStore(reducer)`로 redux store를 생성한다.

  - `getState`를 통해 현재의 state를 가져올 수 있다.

  - `dispatch(action)`으로 store의 reducer에 action을 전달한다.

  - `subscribe(listener)`로 이벤트 리스너 등록을 할 수 있다.

#### 사용 방법

- Reducer 정의

  ```
  // 초기 상태를 설정
  const initialState = { value: 0 };

  // state와 action을 인자로 받아서 새로운 상태를 반환하는 reducer를 작성
  const counterReducer = (state = initialState, action) {
    // 사전에 정의된 action의 타입이 다음과 같다면 새로운 상태를 반환
    if (action.type === "counter/incremented") {
      return {
        ...state,
        value: state.value++
      }
    }
    // 그렇지 않다면, 기존의 state를 반환
    return state
  }
  ```

- store 정의

  ```
  import { createStore } from "redux";
  import counterReducer from "./reducer";

  const store = createStore(counterReducer)
  ```

  - 리액트 앱의 경우, store 객체를 react-redux의 Provider에 전달하여 전역 스토어를 정의한다.

    ```
    import React from "react";
    import { createRoot } from "react-dom/client";
    import App from "./App";
    import { Provider } from "react-redux";

    const root = document.getElementById("root");

    ReactDom.createRoot(root).render(
    <React.StrictMode>
      <Provider store={store}>
        <App />
      </Provider>
    </React.StrictMode>
    );
    ```

- store 메소드 활용 예시

```
console.log(store.getState()) // { value: 0 }

store.dispatch({ type: "counter/incremented" })
console.log(store.getState()) // { value: 1 }
```

### Context API와 Redux의 차이

Context API와 Redux 모두 전역 상태 관리를 위한 도구이며, Redux는 Context API를 가지고 만든 라이브러리이다.

따라서 상태 관리 그 자체의 방법에 따른 차이가 있다기보다, 둘의 성능과 사용이 권장되는 상황 등의 차이가 있다고 할 수 있다.

1. 성능 상의 차이

Redux는 어떤 컴포넌트가 전역 상태의 특정 값을 의존하게 될 때, 해당 값이 바뀔 때에만 리렌더링 되도록 최적화되어 있다.

반면, Context API는 이런 성능 최적화가 이루어져 있지 않기 때문에, 컴포넌트가 특정 값에 의존하게 되는 경우 해당 값과 상관없는 값이 바뀌게 되더라도 컴포넌트의 리렌더링이 발생할 수 있다.

따라서 Context API는 관심사의 분리가 상당히 중요하며, 상태용과 업데이트용 context 분리도 필요하기 때문에 작업해야 하는 일들이 많다.

전역 상태가 고도화/다양화 되는 경우에는 Context API를 사용하는 것이 적절하지 않을 수 있다.

2. 권장 사항

<img src="https://postfiles.pstatic.net/MjAyMjAxMTRfMjk0/MDAxNjQyMTU2OTYzNzk1.V9FoEbRfJ6FbJm5gTPhMS3CMUJndCdgAKunTfnJyLcgg.7p5W20IQYAI39N_g9OQMuTZalL0msYzQvfoiCKZHpbEg.PNG.mitty0304/image.png?type=w966" alt="difference between context api and redux" />

- 기본적으로 Context API는 리액트 내부의 라이브러리이기 때문에 리액트에서만 사용되는 반면, Redux는 UI와 독립적이기 때문에 별도로 사용 가능하다.

- 단순히 prop-drilling 문제만을 해결해야 하는 경우 Context API 사용이 적합하고, context와 useReducer의 조합으로 적당히 복잡한 상태 관리의 구현이 가능하다.

- 만약 앱 자체의 규모가 굉장히 크거나 무겁고 복잡한 경우, 그리고 디버깅 툴, 테스트 코드의 유연한 작성, 미들웨어 등의 추가적인 기능이 필요한 경우, 특정 구성 요소만 리렌더링 시키고 싶다면 Redux 사용이 적합하다.
