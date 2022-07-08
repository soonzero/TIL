# useState와 useRef

일반적으로 리액트에서는 컴포넌트의 상태를 관리하기 위해 `useState()` 훅을 많이 사용한다.

state가 많아진다는 것은 변경될 state도 많아진다는 것이고, 그러다보면 결국 리렌더링도 많이 일어난다는 뜻이 된다.

불필요한 렌더링이 많이 일어난다는 것이다.

생각해보니 프로젝트를 진행할 때 컴포넌트에서 상태로 관리해야겠다 싶으면 useState를 너무 남발해서 사용했다.

그래서 확실히 짚고 넘어가야 할 것들은 정리를 하는 게 좋을 것 같다는 생각에 정리해본다.

<details style="cursor: pointer; border-radius: 8px; border: 1px solid white; padding: 1rem 1rem 1rem 2rem">
<summary>급반성</summary>
최근에 많은 라이브러리나 기술을 배워보겠다며 이것저것 수박 겉핥기 식으로 공부했다.

<br />

나는 리액트를 주로 사용하는 개발자니까 리액트 기초/기반을 단단하게 다진 상태에서 다른 것들을 공부했었어야 하는데

<br />

조급한 마음에 이것저것 건드려본 게 너무 많은 것 같다는 생각이 문득 들어서 어제 밤부터 리액트를 찬찬히 다시 톺아보고 있다.

<br />

리액트 배운지가 거의 반 년이 지났는데도 내가 모르면서 지나간 것들, 새롭게 느껴지는 것들이 많아서 더 반성하게 됐다...

<br />

기본적인 것들을 더 탄탄히 해서 나중에 다른 것을 배울 때 더 잘 흡수할 수 있을 거라는 확신을 갖고 싶다.

</details>

## useState와 useRef의 차이점

useState와 useRef는 둘 다 함수형 컴포넌트에서 상태 관리를 돕는 hook이다.

그럼 왜 두 개가 구분이 되어 있을까?

이 둘의 큰 차이점은 바로 `렌더링의 여부`라고 할 수 있다. useState는 값이 바뀔 때마다 리렌더링이 발생시키는 반면, useRef는 그렇지 않다.

useRef는 객체를 참조하면서 객체 안의 current 속성을 통해 데이터를 관리하기 때문에 내부의 값이 변하더라도 참조형 데이터의 특성상 주소값이 변하지 않는다.

따라서 내부 값의 변동에도 불구하고 변경사항을 감지하지 못해 리렌더링이 발생하지 않는 것이다.

```
export default function App() {
  const [count, setCount] = useState(0);
  const countRef = useRef(0);

  const stateUp = () => {
    setCount(count => count + 1);
  }

  const refUp = () => {
    countRef.current++;
    console.log(countRef.current);
  }

  return (
    <div className="App">
      <div>{count}</div>
      <button onClick={stateUp}>state 증가</button>
      <div>{countRef.current}</div>
      <button onClick={refUp}>ref 증가</button>
    </div>
  );
}
```

위 예시에서 `state 증가` 버튼을 누르면 컴포넌트가 리렌더링되면서 count가 올라가는 것을 볼 수가 있다.

하지만 `ref 증가` 버튼을 누르면 컴포넌트는 리렌더링되지 않고 콘솔에 countRef의 current 값만 출력된다.

refUp 함수 내에서 countRef의 값은 계속해서 증가되지만, 실제 DOM은 0을 나타낸다.

`ref 증가` 버튼을 여러 번 누르고 나서 다시 `state 증가` 버튼을 누르면 컴포넌트가 리렌더링되면서 `countRef`의 current 값이 화면에 렌더링되고,

다시 `ref 증가` 버튼을 누르면 화면에 보이는 `countRef`의 current 값은 더 이상 변하지 않는다.

### useRef의 다른 사용법

useRef를 리렌더링되지 않는 변수처럼 사용할 수 있지만, 특정 DOM을 선택할 때 사용할 수도 있다.

기존에 자바스크립트에서 특정 DOM 엘리먼트를 선택할 때에는 `getElementById`, `querySelector`과 같은 DOM selector 함수를 사용했었다.

리액트에서는 useRef로 Ref 객체를 생성하고 이름을 붙여준 후에 원하는 DOM에 ref 값으로 설정만 해주면 Ref 객체의 current값이 선택한 DOM을 가리킨다.

```
const test = () => {
  const inputRef = useRef();
ㄴ
  useEffect(() => {
    inputRef.current.focus();
  }, [])

  return (
    <div>
      <input type="text" ref={inputRef} />
    </div>
  )
}
```

위 코드에서는 컴포넌트가 마운트되면, 사용자가 input 필드를 클릭하지 않아도 input 태그에 쉽게 접근해서 이벤트 처리를 할 수 있다.

### 정리

useState로 상태를 변경하는 경우 자동으로 컴포넌트가 리렌더링되는데, 이 때 모든 변수가 초기화되고 원하지 않는 렌더링이 발생할 수 있다.

ref값을 아무리 변경해도 컴포넌트는 다시 렌더링을 발생시키지 않기 때문에, 불필요한 렌더링을 방지하기 위해서는 useState 대신 useRef를 사용하면 된다.

뿐만 아니라 useRef로 생성된 ref의 값은 컴포넌트가 여러 번 렌더링되더라도 값에 변동이 없기 때문에 굉장히 유용하게 사용할 수 있다.

다음과 같이 불필요한 렌더링을 발생시키지 않는 값을 관리할 때 useRef를 사용한다.

- `setTimeout`, `setInterval`을 통해서 만들어진 `id`
- 외부 라이브러리를 사용하여 생성된 인스턴스
- scroll 위치
