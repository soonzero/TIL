# Next.js

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

- Image 컴포넌트

  - Next.js는 이미지 최적화를 기본으로 지원하기 때문에, 이미지의 resizing, optimization(최적화)이 가능하다. 작은 viewport를 가진 기기에서는 큰 사이즈의 이미지가 로드되는 것을 방지해준다.

  - 브라우저의 지원 여부에 따라 WebP와 같은 모던 포맷의 이미지도 제공이 가능한데, 자동으로 차세대 이미지 포맷을 채택하고 그 포맷을 지원하는 브라우저에 해당 이미지를 제공해준다.

  - 빌드 타임에 이미지를 최적화 하는 대신, 사용자가 이미지를 요청하는 때에 최적화 작업을 하고, 이미지는 viewport 내에 스크롤될 때 로드된다.

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

- CSS Styling

  - Next.js는 style.jsx, CSS Modules, Sass를 내장 지원하는데, 다른 라이브러리를 사용할 수 있다. (Styled-components, Tailwind, emotion 등)
