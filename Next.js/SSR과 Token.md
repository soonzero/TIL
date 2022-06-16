# Next.js

## SSR과 Token

지금 Next.js로 진행하고 있는 프로젝트에서는, token값을 window 객체의 storage에 저장해 두고 API 호출 때마다 불러와서 사용하고 있었다.

페이지 구성의 윤곽이 어느 정도 잡히고, SSR이나 SSG가 필요한 페이지들을 선택해서 구현하고 있었다.

<hr />

### 오류 내용

그런데 API 호출 시 필요한 token값을 storage에서 불러오는데 에러가 발생했다.

    "ReferenceError: 'localStorage' is not defined"

    "ReferenceError: 'window' is not defined"

### 오류가 발생한 이유

- Next.js는 CSR을 수행하기 전에 SSR을 수행한다.

- Next.js에서 제공하는 SSR에서는 `window`, `document` 등의 브라우저 전역 객체를 사용할 수 없다.

  - `window` 객체는 Client-Side에만 존재하기 때문이다.

<br />

- 따라서, 페이지가 Client-side에 로드되고, `window`의 객체가 정의될 때까지 `localStorage`, `sessionStorage`에는 접근할 수 없는 것이다.

### 해결 방법

1. `typeof window !== "undefined"`

   - 페이지가 클라이언트에 마운트될 때까지 기다렸다가 `localStorage`에 접근해야 한다.

   - `window` 객체가 참조되지 않을 경우, `undefined`를 반환한다.

   - `localStorage`에 접근하기 전에 `localStorage`를 먼저 정의한다.

   ```
   if (typeof window !== "undefined") {
   const item = localStorage.getItem("token");
   }
   ```

2. `useEffect`

   - `useEffect`는 렌더링될 때 실행되므로, 초기 서버 빌드 시 `useEffect` 내부 코드는 실행되지 않는다.

   - `useEffect`는 클라이언트 쪽에서만 실행되므로 `localStorage`에 안전하게 접근할 수 있다.

   ```
   import { useEffect } from "react";

   useEffect(() => {
   const item = localStroage.getItem("token");
   }, [])
   ```

<hr />

### `js-cookie` 사용하기

- `js-cookie`는 쿠키를 처리하기 위한 자바스크립트의 쿠키 라이브러리다.

#### 설치

    npm i js-cookie

#### 기본 사용법

     // 모듈 import하기
     import Cookies from "js-cookie"

     // 쿠키 굽기(기본 = 모든 페이지에서 사용 가능)
     Cookies.set("token", "token12345")

     // 쿠키 굽기(7일 후 만료)
     Cookies.set("token", "token12345", { expires: 7 })

     // 쿠기 굽기(7일 후 만료 + 특정 페이지에서만 사용 가능)
     Cookies.set("token", "token12345", { expires: 7, path: "/search" })

     // 구워진 특정 쿠키 가져오기
     Cookies.get("token") // "token12345"
     Cookies.get("nothing") // "undefined"

     // 모든 쿠키 가져오기
     Cookies.get() // { token: "token12345" }

     // 구워진 특정 쿠키 삭제(기본 = 모든 페이지에서 사용)
     Cookies.remove("token")

     // 구워진 특정 쿠키 삭제(특정 페이지에서만 사용 가능)
     Cookies.remove("token", { path: "/search" })

#### 기본값

- 쿠키는 사용자가 브라우저를 닫을 때 기본적으로 삭제된다.

- 즉, `expires` 속성을 따로 설정하지 않으면 세션 쿠키로 구워진다.

#### 만료 시간을 1일 이내로 설정하려면?

```
// 쿠키 굽기(15분 뒤 만료)
const expireTime = newDate(new Date().getTime() + 15 * 60 * 1000)
Cookies.set("token", "token12345", { expires: expireTime })

// 쿠키 굽기(30분 뒤 만료)
const expireTime = 1/48 // 1/24 => 1시간

// 쿠키 굽기(12시간 뒤 만료)
const expireTime = 0.5;
Cookies.set("token", "token12345", { expires: expireTime })
```

#### [추가 사용법 by js-cookie](https://github.com/js-cookie/js-cookie/tree/latest#readme)
