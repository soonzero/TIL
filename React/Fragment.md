# Fragments

얼마 전 SSR을 공부하다가 어떤 블로그에서 `<Fragment>`라는 태그를 봤다.

처음에는 그냥 Fragment의 사전적 의미인 `조각`을 뜻하는 것 말고는 다른 의미는 딱히 없는 태그인 줄 알고 넘어갔었다.

그리고 Suspense와 Error Boundaries에 관련된 내용을 살펴보기 위해 [리액트 공식 문서](https://ko.reactjs.org/docs/getting-started.html)에 다시 접속했다.

인덱스를 살펴보다가 Fragment에 관한 링크가 있어 '뭐지?' 라는 생각에 들어가서 읽어보았는데 내가 전혀 모르고 사용한 부분이 있었던 것을 깨달았다.

그래서 간략하게 여기에 정리해보려고 한다.

## Fragment

리액트 컴포넌트는 일반적으로 jsx 문법을 가진 element들을 반환하는데,

Fragment를 사용하면 DOM에 별도의 노드를 추가하지 않고 여러 자식을 그룹화할 수 있다.

```
const App = () => {
  return (
    <Fragment>
      <ComponentA />
      <ComponentB />
      <ComponentC />
    </Fragment>
  )
}
```

### 단축 문법

```
const App = () => {
  return (
    <Fragment>
      <ComponentA />
      <ComponentB />
      <ComponentC />
    </Fragment>
  )
}
```

처음에 `<Fragment />`라는 태그를 모르고 빈 태그를 사용하면 된다고 배워서 지금까지 빈 태그를 이용해왔다.

알고 보니 내가 이때까지 사용했었던 빈 태그는 `<Fragment />` 태그를 간단하게 사용할 수 있는 단축 문법이었던 것이다.

그리고 리액트에서는, 컴포넌트의 반환값에 map 함수를 사용할 때에는 key 속성을 추가해주는 것을 권장한다.

```
const Example1 = () => {
  const array1 = [{ ... }, { ... }, ..., { ... }]

  return (
    <div>
      {array.map(item => {
        return (
          <div key={item.id}>
            <h1>{item.title}</h1>
            <p>{item.description}</p>
            ...
          </div>
        )
      })}
    </div>
  )
}
```

위 예시에서 `<h1>`과 `<p>` 태그, 혹은 그 이상의 엘리먼트들을 반환하고 싶다면 빈 태그를 사용하거나 의미 없는 `<div>` 태그로 묶어서 반환해야 한다.

하지만 빈 태그를 사용하게 되면 리액트에서 사용을 권장하는 key 속성을 넣을 수 없기 때문에 `<div>`와 같은 노드를 새로 생성해주는 것이 불가피하다.

불필요한 노드를 생성하는 것은 낭비이므로 이런 경우에 `<Fragment />` 태그를 사용해 별도의 노드를 추가하지 않고 key 속성을 추가해 줄 수 있다.

```
const Example2 = () => {
  const array2 = [{ ... }, { ... }, ..., { ... }]

  return (
    <div>
      {array.map(item => {
        return (
          <Fragment key={item.id}>
            <h1>{item.title}</h1>
            <p>{item.description}</p>
          </Fragment>
        )
      })}
    </div>
  )
}
```

위와 같이 `<Fragment>` 문법을 사용해 명시적으로 선언한다면 key 속성을 전달할 수 있다.

`<Fragment>`는 유일하게 사용할 수 있는 속성이 `key` 속성이고, 추후에 이벤트 핸들러나 추가적인 속성을 지원할 수도 있다고 한다.
