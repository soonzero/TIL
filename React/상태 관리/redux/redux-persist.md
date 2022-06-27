# redux-persist

리덕스로 기능을 구현할 때 항상 드는 생각이 있었다.

왜 리덕스 store는 페이지를 새로고침 할 때 데이터가 날아갈까?

나름 이 문제를 해결하기 위해서 도안한 방법은 다음과 같았다.

페이지를 새로고침할 때마다, 서버에서 불러온 데이터를 store에 set하기

하지만 이 방법을 사용할 경우 불필요한 서버와의 통신이 계속되기 때문에 서버에 부담이 커지고 트래픽도 아까웠다.

다른 대응 방안으로 HTML5에서 지원하는 웹 스토리지에 저장하고자 하는 reducer의 state를 저장하고,

새로고침할 때마다 저장공간에 있던 데이터를 Redux에 불러오는 방법이 있는데 이 작동을 위해 `redux-persist`를 사용할 수 있다.

    npm i redux-persist

## reducer에 persist store 정의하기

- 로컬 스토리지에 저장하려면 `import storage from "redux-persist/lib/storage"`

- 세션 스토리지에 저장하려면 `import storageSession from "redux-persist/lib/storage/session"`

```
import { combineReducers } from "redux";
import { persistReducer } from "redux-persist";
import storage from "redux-persist/lib/storage";

import auth from "./auth";
import board from "./board";
import studio from "./studio";

const persistConfig = {
  key: "root",
  storage, // 로컬 스토리지에 저장
  whitelist: ["auth"] // auth, board, studio 3개의 reducer 중 auth만 로컬 스토리지에 저장
  // blacklist를 사용하면 해당 항목만 제외할 수 있음
}

export const rootReducer = combineReducers({
  auth,
  board,
  studio
});

export default persistReducer(persistConfig, rootReducer);
```

## persist store 사용

```
// src/index.js

import React from "react";
import ReactDOM from "react-dom/client";
import { createStore, applyeMiddleware, compose } from "redux";
import { Provider } from "react-redux";
import { persistStore } from "react-redux";
import { PersistGate } from "redux-persist/integration/react";
import App from "./App";
import configureStore from "./store";
import { rootReducer } from "./reducers";

const store = createStore(rootReducer);
const persistor = persistStore(store);

const root = ReactDOM.createRoot(document.getElementById("root"))

root.render(
  <Provider store={store}>
    <PersistGate loading={null} persistor={persistor}>
      <App />
    </PersistGate>
  </Provider>
);
```
