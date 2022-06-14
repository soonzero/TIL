## CSR, SSR과 Next.js

### SPA와 MPA

- SPA(Single Page Application)

  하나의 HTML 파일을 기반으로, 자바스크립트를 이용해 동적으로 화면의 컨텐츠를 바꾸는 방식의 웹 애플리케이션이다.

- MPA(Multiple Page Application)

  사용자가 페이지를 요청할 떄마다 웹 서버가 요청한 UI와 필요한 데이터를 HTML로 파싱해서 보여주는 방식의 웹 애플리케이션

<br />

- 전통적인 방식을 이용한다면, SPA가 사용하는 렌더링 방식이 CSR이고, MPA가 사용하는 렌더링 방식이 SSR이다.

<hr />

### CSR(Client Side Rendering)

<img src="https://github.com/baeharam/Must-Know-About-Frontend/raw/main/images/frontend/CSR.png" alt="CSR">

<br />

- 브라우저가 서버에 요청한 HTML과 자바스크립트 파일이 로드되면, 사용자의 상호작용에 따라 JS를 이용해서 동적으로 렌더링을 시킨다.

<br />

**👍 장점**

- 첫 로딩만 기다리면 동적으로 빠르게 렌더링되기 때문에 사용자 경험 면에서 좋다.

- 서버에게 리소스를 요청하는 횟수가 훨씬 적기 때문에 서버의 부담이 덜하다.

**👎 단점**

- 처음 페이지 접속 시, 모든 스크립트 파일이 로드될 때까지 기다려야 한다.

  - 리소스를 청크(Chunk) 단위로 묶어서 요청할 때에만 다운받도록 할 수는 있으나 완벽한 해결은 어렵다.

- 검색엔진의 검색 봇이 크롤링을 하는 데에 어려움을 겪기 때문에 SEO(Search Engine Optimization), 즉 검색엔진 최적화에 문제가 발생한다.

  - 구글 봇의 경우 JS를 지원하지만, 다른 검색 엔진은 지원하지 않기 때문에 문제가 될 수 있다.

  - 이러한 문제점을 해결하기 위해 prerender.io 와 같은 서비스를 사용할 수 있다.

<hr />

### CSR(Client Side Rendering)

<img src="https://github.com/baeharam/Must-Know-About-Frontend/raw/main/images/frontend/SSR.png" alt="SSR">

<br />

- 브라우저가 페이지를 요청할 때마다 해당 페이지에 관련된 HTML, CSS, JS 파일을 비롯한 데이터를 받아와서 렌더링 시킨다.

<br />

**👍 장점**

- 초기 로딩 속도가 굉장히 빠르기 떄문에 사용자가 컨텐츠를 빨리 확인할 수 있다.

- JS를 이용한 렌더링이 아니기 때문에 SEO에 유리하다.

**👎 단점**

- 매번 페이지를 요청할 때마다 새로고침 되기 때문에 사용자 경험 측에서 사용자에게 부정적 경험을 심어줄 가능성이 있다.

- 서버에 매번 요청을 해야 하는데, 중복된 리소스도 함께 요청하고 수신해야 하기 때문에 서버의 부담이 큰 데다가 불필요한 트래픽의 낭비가 심하다.

<hr />

### Next.js (간략)

- 위와 같은 장/단점 때문에 요즘에는 첫 웹 페이지가 보일 때에는 SSR을 적용하고, 이후에는 CSR을 적용하는 방법을 사용한다고 한다.

- React를 SSR로 쉽게 구현할 수 있도록 환경을 제공해주는 프레임워크가 있는데 그게 바로 **Next.js**다.

- Next.js를 사용하면, CSR의 단점을 보완하고 SSR의 장점을 살릴 수 있다.

  - 웹 페이지 첫 진입 시 비교적 빠른 로딩 가능
  - 검색 엔진 최적화가 가능
  - 코드 스플리팅으로 모든 페이지가 아닌 방문한 페이지만의 데이터 수신 가능

    - 코드 스플리팅(Code Splitting): Next.js는 리소스들의 import된 것들을 분석해서, 로딩한 페이지가 꼭 필요로 하는 JS 파일만 로드하기 때문에, 첫 페이지 로드가 굉장히 빠르다. 다만 사이트에서 자주 사용되는 JS 파일은 main JS bundle로 이동된다.

<br />

**Next.js를 여기서 모두 다루기에는 양이 방대하다. 다음에 Next.js에 대해서 [자세히](https://github.com/soonzero/TIL/blob/main/Next.js) 공부해봐야겠다.**

<hr />
