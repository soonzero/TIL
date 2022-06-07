# Next.js

Next.js를 사용해서 간단한 서비스를 만들어보고 있었다.

Next.js가 React 위에서 사용할 수 있는 프레임워크라, 이전에 하던 것처럼 쓰면 될 줄 알았다.

svg 파일을 ReactComponent로 import 해와서 컴포넌트로 사용하려고 하니 오류가 발생한다.

<img src="https://user-images.githubusercontent.com/95613159/172409626-263b1bcc-1d90-42e7-9a6f-07d2c6d9740c.png" alt="error" />

<hr />

## SVG(Scalable Vector Graphics)

SVG 파일은 아이콘의 색, 크기 등 요소를 디자인에 따라 바꿀 수 있는 파일로, 일종의 **벡터 형식** 이미지 포맷이다.

- 벡터 형식: 모양을 정의하는 텍스트 집합을 이미지로 취급한다. 대표적으로 EPS 형식이 벡터 형식이다.

  - 확대와 축소에 강하고 화질 열화가 없어 어떤 화면에서도 선명한 이미지를 표시할 수 있다.

  - 파일 크기도 작기 때문에 리소스에 한계가 있는 경우에도 사용이 용이하다는 장점이 있다.

<br />

### React에서의 SVG 사용

일단 이전에 CRA에서는 SVG 파일을 리액트 컴포넌트로 import 해서 사용했고, 전혀 문제가 없었다.

```
import { ReactComponent as Logo } from "./logo.svg"

...

const NavBar = () => {

  ...

  return (
    <nav>
      <Logo />
    </nav>
  )
}
```

<br />

### Next.js에서의 SVG 사용

위 방법을 똑같이 사용해서 Next.js 프로젝트에 적용해보니

```
Module parse failed: Unexpected token (1:0)
You may need an appropriate loader to handle this file type
```

위와 같은 오류를 뿜어냈다.

웹팩에는 svg파일을 비롯한 특정 파일 형식을 핸들링할 수 있는 `loaders`가 따로 없다. 따라서 JS 코드가 아닌 파일을 전처리하려면 `loaders`가 필요하다.

1.  `svgr` 사용하기

    - `svgr`을 사용하기 위해 `@svgr/webpack` 패키지를 설치한다.

      ```
      npm install -D @svgr/webpack
      ```

    - 그리고 `next.config.js`를 프로젝트 루트에 작성하여 기존 설정을 수정할 수 있도록 해야한다.

      ```
      /** @type {import('next').NextConfig} */

      const nextConfig = {
        reactStrictMode: true,
        webpack: (config) => {
          config.module.rules.push({
            test: /\.svg$/i,
            use: ["@svgr/webpack"],
          });
          return config;
        },
      };

      module.exports = nextConfig;
      ```

    - 이렇게 하면 평소처럼 SVG 파일을 import해서 사용할 수 있다. 다만, 다음과 같이 import한다.

      ```
      import Logo from "./Logo.svg"
      ```

2.  `babel-plugin-react-svg` 사용

    - 웹팩 loader보다 babel 플러그인을 선호한다면, `babel-plugin-inline-react-svg`을 사용해서 SVG 파일을 컴포넌트로 import할 수 있다.

      ```
      npm install -D babel-plugin-inline-react-svg
      ```

    - 패키지를 설치하고, `.babelrc` 파일을 다음과 같이 수정해준다.

      ```
      {
        "presets": ["next/babel"],
        "plugins": ["inline-react-svg"]
      }
      ```
