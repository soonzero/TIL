# Next.js

    - Next.js를 사용하면, CSR의 단점을 보완하고 SSR의 장점을 살릴 수 있다.

      - 웹 페이지 첫 진입 시 비교적 빠른 로딩 가능
      - 검색 엔진 최적화가 가능
      - 코드 스플리팅으로 모든 페이지가 아닌 방문한 페이지만의 데이터 수신 가능

        - 코드 스플리팅(Code Splitting):
        Next.js는 리소스들의 import된 것들을 분석해서 로딩한 페이지가 꼭 필요로 하는 JS 파일만 로드하기 때문에, 첫 페이지 로드가 굉장히 빠르다.
        다만 사이트에서 자주 사용되는 JS 파일은 main JS bundle로 이동된다.

from [CSR과 SSR](https://github.com/soonzero/TIL/blob/main/프론트엔드/CSR과%20SSR.md)

<br />

## 페이지 전환 (Navigate Between Pages)

- Fast Refresh: Next.js는 파일 내용의 변화를 감지하고, 자동으로 브라우저에 변동 사항을 적용시켜서 새로고침 없이도 렌더링한다.

- File System Routing: Next.js는 페이지의 개념에 기반한 파일 시스템을 갖고 있으며, 이를 이용해서 라우팅이 가능하다.

  - 정적 라우팅: pages 폴더의 하위에 파일을 생성하는 것만으로 경로 설정이 가능하다.

  - 동적 페이지 라우팅: 파라미터와 대괄호 문법을 사용해서 가능하다.

  - 아래처럼 Link를 import 하고, 컴포넌트처럼 사용이 가능하다. attribute를 추가해야 할 때는 Link 태그 안의 <strong>`<a>` 태그</strong>에 추가해야 한다.

    ```
    import Link from "next/link";

    ...
      return (
        <Link href="/">
          <a className="home">Home</a>
        </Link>
      )
    }
    ```

- Client-Side Navigation(CSV): Link 컴포넌트를 사용함으로써 브라우저에서 수행하는 기본 내비게이션보다 빠른 JS를 사용한 페이지 전환이 발생한다.

  - 기본 브라우저는 href attribute를 통해 다른 페이지로 이동할 때에는 전체 페이지를 새롭게 재렌더링하는데, CSV는 일부분만 새롭게 렌더링한다.

- Code Splitting: Next.js는 코드를 분해해서, 각각 페이지에서 필요한 요소만 불러온다. 이를 통해 수많은 페이지를 가진 앱을 실행하더라도 홈 페이지를 굉장히 빠르게 불러올 수 있다는 장점이 있다.

  - 특정 페이지에서만 필요한 코드를 불러온다는 것은 다른 페이지들과의 관계성이 없다는 의미이므로, 그 페이지에서 오류가 발생하더라도 앱의 다른 페이지들은 오류 없이 동작한다는 의미이다.

- Prefetching: Link 컴포넌트가 브라우저의 viewport에 나타날 때, Next.js는 연결될 페이지를 백그라운드에서 prefetch한다. 다른 페이지로 이동할 수 있는 Link 컴포넌트에 해당하는 요소를 클릭하면, 백그라운드에서 이미 로딩이 완료된 페이지를 곧바로 보여준다.

<br />

## 리소스, 메타데이터와 CSS (Assets, Metadata, and CSS)

- Image 컴포넌트

  - Next.js는 이미지 최적화를 기본으로 지원하기 때문에, 이미지의 resizing, optimization(최적화)이 가능하다. 작은 viewport를 가진 기기에서는 큰 사이즈의 이미지가 로드되는 것을 방지해준다.

  - 브라우저의 지원 여부에 따라 WebP와 같은 모던 포맷의 이미지도 제공이 가능한데, 자동으로 차세대 이미지 포맷을 채택하고 그 포맷을 지원하는 브라우저에 해당 이미지를 제공해준다.

  - 빌드 타임에 이미지를 최적화 하는 대신, 사용자가 이미지를 요청하는 때에 최적화 작업을 하고, 이미지는 viewport 내에 스크롤될 때 로드된다.

<br />

- Head 컴포넌트

  - 메타 데이터를 수정하기 위해서 Head 컴포넌트를 사용할 수 있다.

    ```
    import Head from "next/head";

    ...

      return (
        <Head>
          <title>Title</title>
        </Head>
      )
    }
    ```

  - 페이지마다 다른 title을 사용하고 싶다면, 이 방법을 사용할 수 있다.

    ```
    // Seo.js

    import Head from "next/head";

    export default function Seo({ title }) {
      return (
        <Head>
          <title>{title} | Next.js</title>
        </Head>
      )
    }
    ```

    ```
    // pages / posts / [id].js

    import Seo from "../../components/seo";

    ...

    export default function Post({ postData }) {
      return (
        <Layout>
          <Seo title={postData.title} />
          <article>
            ...
          </article>
        </Layout>
      )
    }
    ```

<br />

- Script 컴포넌트

  - 메타데이터 이외에 즉시 로드되고 실행되어야 하는 스크립트도 일반적으로 페이지의 `<head>`태그 안에 추가하며, `<script>` 요소를 사용한다.

  - Next.js에서는 Script 컴포넌트를 사용할 수 있다.

    ```
    import Head from "next/head";

    ...

      return (
        <Head>
          <title>Title</title>
        </Head>
        <Script
          src="https://connect.facebook.net/en_US/sdk.js"
          strategy="lazyOnload"
          onLoad={() => console.log("스크립트 로드 완료")}
        />
      )
    }
    ```

  - `strategy`는 언제 써드파티 스크립트를 로드할 지를 제어할 수 있다. `lazyOnload`는 이 스크립트를 브라우저의 쉬는 시간에 불러오도록 하는 명령이다.

  - `onLoad`는 스크립트의 로딩이 끝나는 즉시 실행시킬 코드이다. 위 예제에서는 스크립트가 로드 완료되었다는 텍스트를 콘솔창에 출력하도록 했다.

<br />

- CSS Styling

  - Next.js는 style.jsx, CSS Modules, Sass를 내장 지원하는데, 다른 라이브러리를 사용할 수 있다. (Styled-components, Tailwind, emotion 등)

<br />

## Base Path

- Next.js에서 base path를 설정하려면 `next.config.js` 파일에서 `basePath` 속성을 설정하면 된다.

  ```
  module.exports = {
    basePath: "/main",
  }
  ```

- 이렇게 설정하면 `<Link>` 태그에서 이동할 때 `/about`이 아닌 `/main/about` 으로 이동하게 된다.

- 단, `<Image />`나 `<svg>` 태그의 `src` 경로에는 적용되지 않으므로 주의해야 한다.

<br />

## Redirects()

- redirection 하기 위해서는 `nest.config.js` 파일에서 `redirects` 키를 사용하면 된다.

  ```
  // next.config.js

  module.exports = {
    basePath: "/docs",
    async redirects() {
      return [
        {
          source: "/old-blog/:path*",
          destination: "/new-blog/:path*",
          permanent: false,
        }
      ]
    }
  }
  ```

- `redirects` 함수는 비동기 함수이며, `source`, `destination`, `permanent` 등을 속성으로 가지는 객체 배열을 return 한다.

  - properties

    - source: incoming request

    - destination: redirect 할 경로

    - permanent: true일 경우 http 코드가 308(permanent)이 되고, false인 경우 http 코드가 307(temporary)이 된다.

    - basePath: true인 경우, redirection 경로의 basePath가 적용되고, false인 경우 적용되지 않는다.

    - has: object 타입이며 header, cookie, query 매칭을 할 때 사용된다.

<br />

## Rewrites()

- Redirect의 경우 source의 경로로 request가 오면 다른 경로로 redirect를 시켜주기 때문에, URL에 source의 경로가 잠깐 보였다가 redirection 경로로 변경된다.

- 하지만 Rewrites의 경우 source 경로를 destination 경로로 매핑하기 때문에 URL 변화 없이 바로 destination 경로로 변경된다.

  ```
  const API_KEY = process.env.API_KEY;

  module.exports = {
    async rewrites() {
      return [
        {
          source: "/api/movies",
          destination: `https://.../movies/${API_KEY}`,
        }
      ]
    }
  }
  ```

  <br />

## Customize 404

- 아래 파일을 만들어서 404 에러 페이지를 커스터마이징할 수 있다.

  ```
  // pages / 404.js

  export default function NotFound() {
    return "Error! 404";
  }
  ```
