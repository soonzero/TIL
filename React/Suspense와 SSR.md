# Suspense와 SSR

이번에 새로운 프로젝트를 시작하려고 준비 중이다.

아직 윤곽이 제대로 잡힌 건 아니지만, 첫 번째 미팅 때 팀원들 각자 어떤 기술 스택으로 진행하고 싶은지 살짝 얘기를 나눠봤다.

나는 일단 리액트를 사용하는 건 확실하기 때문에, 정적 언어인 타입스크립트를 한 번 사용해보려고 한다.

내가 퍼블리싱을 대부분 맡을 예정이라 저번 개인 프로젝트 때 인상 깊게 사용했었던 tailwind를 사용할 것 같다.

그리고 SEO 최적화를 제대로 연습해보고 싶어서 Next.js를 사용하겠다고 얘기했었는데,

최근(까지는 아니지만)에 릴리즈된 리액트18 버전부터는 Suspense를 이용해서 SSR을 구현할 수 있다는 걸 알게 됐다.

## React 17 SSR

이전 리액트의 SSR은 아래 단계로 이루어진다고 한다.

<img src="https://miro.medium.com/max/1400/1*jJkEQpgZ8waQ5P-W5lhxuQ.png" alt="react-SSR" />

1. 서버에서 페이지를 그리는 데 필요한 데이터들을 fetch한다.

2. 서버사이드에서 데이터를 처리하고, HTML을 브라우저로 보내면 사용자가 화면을 확인할 수 있지만 조작은 불가능하다.

3. 브라우저에서 필요한 자바스크립트 코드를 다운받는다.

4. 브라우저가 서버사이드에서 렌더링된 HTML 파일에 자바스크립트 파일을 연결(Hydration)시킨다.

5. 페이지가 인터랙티브 상태가 된다.

이 전체 과정이 폭포수 떨어지듯 동기적으로 이루어진다고 해서 **Waterfall 모델**의 형태라고 한다.

하지만 전체 컴포넌트 중 특정 부분에서 시간이 오래 소요되면, 그 부분이 모두 처리될 때까지 사용자는 빈 화면을 두고 기다려야 한다.

물론 좋은 사양을 가진 컴퓨터는 비교적 덜하겠지만, 저사양 환경의 컴퓨터에서는 시간이 많이 소요될 수 있고

이는 곧 나쁜 사용자 경험을 제공하는 원인이 된다.

- 전통적 방식의 SSR(Waterfall Rendering): 사용자에게 처음 보여지는 페이지 전체를 그려서 내려주는 방식(Top-Down)

<div style="display: flex;">

<img src="https://velog.velcdn.com/images%2Fjay%2Fpost%2F0bac3292-8e6c-4e0f-ad8b-6130793333a3%2F1.png" alt="react-SSR-waterfall-top" width="50%" />
<img src="https://velog.velcdn.com/images%2Fjay%2Fpost%2Fbc0bc1e6-7656-45fc-bc38-3584bba47777%2F2.png" alt="react-SSR-waterfall-down" width="50%"/>

</div>

<hr />

## React 18 SSR

하지만 리액트18 버전부터는 유저에게 처음 보여지는 페이지 전체를 그려 내려주지 않고,

빠르게 준비되는 부분부터 렌더링/수화 시켜주며 이 기능을 `Streaming HTML`과 `Selective Hydration`이라고 부른다.

### HTML Streaming

서버에서 html을 보내주는 것을 `html streaming`이라고 하는데,

`renderToString`을 사용한 전통적 방식으로 SSR을 구현하면 브라우저는 서버에서 보내주는 완성된 html 페이지를 통째로 받았다.

<img src="https://user-images.githubusercontent.com/95613159/176862334-e6ae2b3f-3c37-4940-8b39-35a36d09a714.png" alt="renderToString and pipeToNodeWritable" />

<del>새로운 버전에서는 서버가 `pipeToNodeWritable`을 이용해 html 코드를 잘개 쪼개서 나누어 보내준다.</del>

새로운 버전에서는 서버가 `renderToPipeableStream`을 이용해 html 코드를 잘개 쪼개서 나누어 보내준다.

```
<Layout>
  <NavBar />
  <SideBar />
  <RightPane>
    <Post />
    <Suspense fallback={<Spinner />}>
      <Comments />
    </Suspense>
  </RightPane>
</Layout>
```

### `<Suspense>`

전체 앱 중 첫 페이지를 렌더링하는 데에 시간이 오래 걸리는 `<Comments />` 컴포넌트를 `<Suspense>`로 감싸주면,

해당 컴포넌트를 렌더링할 준비가 되기 전까지 fallback props로 넘겨준 `<Spinner />`를 보여준다.

<img src="https://velog.velcdn.com/images%2Fjay%2Fpost%2Ffa9c9c65-b576-4263-b1e8-37b51f00a8a5%2F3.png" alt="status of page with spinner instead of comments" />

<br />

그러면 실제로 사용자는 위와 같은 페이지를 보게 되는 것이다.

```
<main>
  <nav>
    <!--NavBar -->
    <a href="/">Home</a>
   </nav>
  <aside>
    <!-- Sidebar -->
    <a href="/profile">Profile</a>
  </aside>
  <article>
    <!-- Post -->
    <p>Hello world</p>
  </article>
  <section id="comments-spinner">
    <!-- Spinner -->
    <img width=400 src="spinner.gif" alt="Loading..." />
  </section>
</main>
```

실제로는 `<Comments />` 대신 `<Spinner />`가 자리잡고 있다가 서버에서 `<Comments />` 컴포넌트를 렌더링할 준비가 모두 끝나면,

html 코드를 추가로 스트리밍하고, fallback element인 `<Spinner />`를 대체한다.

```
<div hidden id="comments">
  <!-- Comments -->
  <p>First comment</p>
  <p>Second comment</p>
</div>
<script>
  // 아래 과정은 실제 과정을 단순화해서 나타낸 것이다.
  document.getElementById('sections-spinner').replaceChildren(
    document.getElementById('comments')
  );
</script>
```

이전 버전까지는 페이지의 모든 요소가 준비되기 전까지는 사용자가 페이지의 일부분도 전혀 보기 어려웠다.

만약 보기 위해서는 `isLoading`과 같은 state를 만들고, 조건에 따라 렌더링할 수 있도록 코드를 더 관리했었어야 했다.

준비된 부분부터 먼저 보는 것이 가능하다는 것은 전반적인 렌더링 과정에 있어서 FTTB(First Time To Bye) 시간이 줄어든다는 것을 의미한다.

이를 통해 렌더링 퍼포먼스와 사용자 경험 모두 향상시킬 수 있다.

사실 `<Suspense>` 태그는 리액트18 버전에서 새로 소개된 것은 아니고, 이전부터 코드 스플리팅을 위해 사용하며 렌더링 되는 데에 소요되는 시간을 아낄 수 있었다.

하지만 SSR을 구현할 때 사용되었던 `renderToString`과 함께 사용할 수 없었기 때문에 서드파티 라이브러리를 사용했어야 했다.

<hr />

### Selective Hydrating(선택적 수화)

위 예시에서 `<Suspense>`로 감싸진 `<Comments />` 컴포넌트를 제외한 모든 요소들이 JS 코드까지 다운받아 수화할 준비를 모두 끝냈다면,

`<Comments />`의 로딩 여부에 관계 없이 다른 요소들은 hydration을 시작할 수 있다.

<img src="https://velog.velcdn.com/images%2Fjay%2Fpost%2Ffa9c9c65-b576-4263-b1e8-37b51f00a8a5%2F3.png" alt="status of page with spinner instead of comments" />

<img src="" alt="" />

기존에는 모든 페이지를 인터랙티브한 상태로 만들기 위해서는 `<Comments />`의 로딩을 기다려야 했다.

<div style="display: flex;">
  <img src="https://user-images.githubusercontent.com/95613159/176873263-19cc0534-68de-4814-8c49-a13b18f3df51.png" alt="selective hydration-1" />
  <img src="https://user-images.githubusercontent.com/95613159/176870149-451aca45-a21d-4ea8-b054-d017b66632c3.png" alt="selective hydration-2" />
</div>

<br />

이제는 selective hydration 덕분에 `<Comments />` 로딩이 끝나지 않더라도 다른 페이지로 이동하는 등의 인터랙션을 제공할 수 있다.

또한 사용자의 인터랙션에 따라 어떤 것을 먼저 hydration시킬 지에 대한 우선순위도 정할 수 있게 되었다.

```
<Layout>
  <NavBar />
  <Suspense fallback={<Spinner />}>
    <Sidebar />
  </Suspense>
  <RightPane>
    <Post />
    <Suspense fallback={<Spinner />}>
      <Comments />
    </Suspense>
  </RightPane>
</Layout>
```

위 예시처럼 `<Sidebar />`과 `<Comments />` 모두 `<Suspense>`로 둘러싸여 있다면 Hydration은 일반적으로 돔트리에 따라 순차적으로 진행된다.

<div style="display: flex;">
<img src="https://user-images.githubusercontent.com/95613159/176870348-cba2d39e-8a99-4415-90d2-8284ecb856c5.png" alt="sidebar-hydrating first" />

<img src="https://user-images.githubusercontent.com/95613159/176870372-85ce81fe-bbf7-4334-b6e4-18aa3cd6cc1c.png" alt="click event occurs on comments before hydration of sidebar is completed" />
</div>
<br />

이 때 사용자가 `<Sidebar />`가 hydration 되기 전에 `<Comments />`에 관심을 가지고 버튼 클릭을 발생시켰다면,

리액트 자체적으로 해당 클릭 이벤트를 기록하고 `<Comments />` 부분에 대한 hydration을 우선적으로 진행한다.

<div style="display: flex;">
<img src="https://user-images.githubusercontent.com/95613159/176870389-10f1abfe-c3d1-471b-b328-b0a3eeb8d066.png" alt="comments hydrating before sidebar" />

<img src="https://user-images.githubusercontent.com/95613159/176870403-280c9a43-6737-40d5-9472-b10d216c231d.png" alt="sidebar hydrating after comments" />
</div>

<br />

그리고 `<Comments />`에 대한 hydration이 완료되면, 그 이후에 `<Sidebar />`의 처리도 마저 진행한다.

<br />

`<Suspense>`를 부모 자식 관계를 가진 컴포넌트를 대상으로 적용시키면 선택적 수화 기능의 장점이 더 확실하게 느껴진다.

```
<Layout>
  <NavBar />
  <Suspense fallback={<BigSpinner />}>
    <Suspense fallback={<SidebarGlimmer />}>
      <Sidebar />
    </Suspense>
    <RightPane>
      <Post />
      <Suspense fallback={<CommentsGlimmer />}>
        <Comments />
      </Suspense>
    </RightPane>
  </Suspense>
</Layout>
```

사용자가 `<Comments />` 컴포넌트 내에 있는 `<Comment />`들 중 첫 번째 요소를 먼저 클릭했다면,

클릭한 요소를 둘러싸고 있는 `<Suspense>` 중 최상위 부모 컴포넌트부터 hydration을 시작하게 된다.

인터랙션과 관계 없는 `<Suspense>`로 둘러싸인 형제 요소들은 hydration을 스킵하고, 인터랙션이 발생한 요소부터 실행하기 때문에 hydration이 즉시 일어나는 것 같은 느낌을 줄 수 있다.

<img src="https://user-images.githubusercontent.com/95613159/176870428-fc685696-c6f0-47c2-a6ce-57f75bf66080.png" alt="selective hydration with parent-children components" />
