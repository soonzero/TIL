# useMemo와 useCallback, React.memo

위 코드들은 리액트에서 최적화를 할 수 있는 코드로 알고 있다.

프로젝트 하나가 무거워지면 사소한 것 하나라도 최적화할 수 있는 것이 좋다.

물론 리액트 공식 문서에서도 무거운 연산이 아니면 굳이 사용하지 않는 것을 추천한다고는 하지만,

언제 무거운 프로젝트를 돌릴 지 모르니까 미리 공부를 해둬야겠다.

## useMemo, useCallback

useMemo와 useCallback을 사용하면 함수 컴포넌트의 작업을 최적화할 수 있다.

그러면 두 코드는 어떤 점에서 차이가 있을까?

### useMemo

useMemo는 함수 컴포넌트의 내부 연산을 최적화해서, `Memoization`된 값을 반환한다.

- Memoization: 동일한 계산을 반복해야 할 때, 이전에 계산한 값을 메모리에 저장함으로써 동일한 계산의 반복 수행을 제거하여 프로그램 실행 속도를 빠르게 하는 기술을 말한다.

```
const memoizedValue = useMemo(() => someFunc(value), [value])
```

- someFunc(): 최적화하고자 하는 함수

- \[value\]: 렌더링 과정에서 이 값이 변했을 때에만 연산을 실행한다. 값은 꼭 배열에 넣어야 한다.

  - 만약 값이 바뀌지 않았다면 이전에 연산했던 결과를 다시 사용한다.

아래는 useMemo()를 사용하는 예시이다.

```
import React, { useRef, useState } from 'react';
import UserList from './UserList';
import CreateUser from './CreateUser';

function countActiveUsers(users) {
  console.log('활성 사용자 수를 세는중...');
  return users.filter(user => user.active).length;
}

function App() {
  const [inputs, setInputs] = useState({
    username: '',
    email: ''
  });

  const { username, email } = inputs;

  const onChange = e => {
    const { name, value } = e.target;
    setInputs({
      ...inputs,
      [name]: value
    });
  };

  const [users, setUsers] = useState([
    {
      id: 1,
      username: 'velopert',
      email: 'public.velopert@gmail.com',
      active: true
    },
    {
      id: 2,
      username: 'tester',
      email: 'tester@example.com',
      active: false
    },
    {
      id: 3,
      username: 'liz',
      email: 'liz@example.com',
      active: false
    }
  ]);

  const nextId = useRef(4);

  const onCreate = () => {
    const user = {
      id: nextId.current,
      username,
      email
    };
    setUsers(users.concat(user));
    setInputs({
      username: '',
      email: ''
    });
    nextId.current += 1;
  };

  const onRemove = (id) => {
    // user의 id가 id와 다른 것을 걸러낸다 = 선택한 id를 가진 user 요소를 없앤다
    setUsers(users.filter(user => user.id !== id));
  };

  const onToggle = (id) => {
    // users를 한 바퀴 돌면서, 선택한 id를 가진 user의 active 속성을 toggle하고 나머지는 그대로 둔다.
    setUsers(
      users.map(user => user.id === id ? { ...user, active: !user.active } : user)
    );
  };

  // const count = countActiveUsers(users);
  // 위 코드를 사용하면, 컴포넌트가 리렌더링될 때마다 연산이 계속해서 일어난다.

  // 아래 코드를 사용하면 컴포넌트가 리렌더링되더라도 두 번째 인자인 deps로 들어간 users의 값에 변동이 있는지 확인하고
  // 변동이 없다면 이전 값을 그대로 사용하고, 변동이 있으면 첫 번째 인자인 함수를 다시 실행해서 새로운 값을 반환한다.
  const count = useMemo(() => countActiveUsers(users), [users]);

  return (
    <>
      <CreateUser
        username={username}
        email={email}
        onChange={onChange}
        onCreate={onCreate}
      />
      <UserList users={users} onRemove={onRemove} onToggle={onToggle} />
      <div>활성사용자 수 : {count}</div>
    </>
  );
}

export default App;
```

### useCallback

useCallback은 위에서 설명한 useMemo와 비슷한 Hook이다.

useMemo가 `특정 결과값`을 재사용하고 싶을 때 사용하는 반면, useCallback은 `특정 함수`를 재사용하고 싶을 때 사용한다.

위 예시에서 사용된 `onCreate()`, `onRemove()`, `onToggle()` 함수는 컴포넌트가 렌더링될 때마다 새로 만들어진다.

```
import React, { useRef, useState, useMemo, useCallback } from 'react';
import UserList from './UserList';
import CreateUser from './CreateUser';

function countActiveUsers(users) {
  console.log('활성 사용자 수를 세는중...');
  return users.filter(user => user.active).length;
}

function App() {
  const [inputs, setInputs] = useState({
    username: '',
    email: ''
  });

  const { username, email } = inputs;

  const onChange = useCallback(
    e => {
      const { name, value } = e.target;
      setInputs({
        ...inputs,
        [name]: value
      });
    },
    [inputs]
  );

  const [users, setUsers] = useState([
    {
      id: 1,
      username: 'velopert',
      email: 'public.velopert@gmail.com',
      active: true
    },
    {
      id: 2,
      username: 'tester',
      email: 'tester@example.com',
      active: false
    },
    {
      id: 3,
      username: 'liz',
      email: 'liz@example.com',
      active: false
    }
  ]);

  const nextId = useRef(4);

  const onCreate = useCallback(() => {
    const user = {
      id: nextId.current,
      username,
      email
    };
    setUsers(users.concat(user));

    setInputs({
      username: '',
      email: ''
    });
    nextId.current += 1;
  }, [users, username, email]);

  const onRemove = useCallback(
    id => {
      // user.id 가 파라미터로 일치하지 않는 원소만 추출해서 새로운 배열을 만듬
      // = user.id 가 id 인 것을 제거함
      setUsers(users.filter(user => user.id !== id));
    },
    [users]
  );

  const onToggle = useCallback(
    id => {
      setUsers(
        users.map(user =>
          user.id === id ? { ...user, active: !user.active } : user
        )
      );
    },
    [users]
  );

  const count = useMemo(() => countActiveUsers(users), [users]);

  return (
    <>
      <CreateUser
        username={username}
        email={email}
        onChange={onChange}
        onCreate={onCreate}
      />
      <UserList users={users} onRemove={onRemove} onToggle={onToggle} />
      <div>활성사용자 수 : {count}</div>
    </>
  );
}

export default App;
```

useCallback 훅을 사용할 때 주의점은, 함수 내에서 사용되는 state나 props가 있다면, 꼭 두 번째 인자인 deps 배열 내에 포함시켜야 한다는 것이다.

만약 넣지 않으면 함수 내에서 해당 값들을 참조할 때 가장 최신의 값을 참조할 거라는 보장이 없다. props로 받아온 함수도 deps 배열에 꼭 넣어줘야 한다.

useCallback은 useMemo를 기반으로 만들어졌다고 한다. useMemo와 useCallback의 차이가 재사용하고자 하는 `값`과 `함수`라는 차이만 있기 때문에,

useCallback을 사용하지 않고 아래처럼 사용해도 가능하지만 더욱 편하게 사용할 수 있도록 나온 것 뿐이다.

```
const someFunc = useMemo(() => () => {
  ...
}, [users])
```

이렇게 useCallback을 사용하면 함수를 새로 선언하지 않고, 재사용할 수 있다.

하지만 이 작업만으로 최적화가 눈에 띄게 발생하지는 않는다.

## React.memo

컴포넌트의 props가 바뀌지 않는다면 리렌더링을 방지해서 컴포넌트의 리렌더링 성능을 최적화할 수 있는데,

그걸 해줄 수 있는 게 바로 React.memo다.

이 함수를 사용하면 컴포넌트에서 **리렌더링이 필요한 상황에서만** 리렌더링을 하도록 설정해줄 수 있다.

```
import React from 'react';

const CreateUser = ({ username, email, onChange, onCreate }) => {
  return (
    <div>
      <input
        name="username"
        placeholder="계정명"
        onChange={onChange}
        value={username}
      />
      <input
        name="email"
        placeholder="이메일"
        onChange={onChange}
        value={email}
      />
      <button onClick={onCreate}>등록</button>
    </div>
  );
};

export default React.memo(CreateUser);
```

이렇게 export 할 때 React.memo로 해당 컴포넌트를 감싸주기만 하면 된다.

그러면 input을 수정할 때 UserList가 리렌더링이 되지 않는 것을 볼 수 있다.

그런데 User 중 하나라도 수정하면 모든 User들이 리렌더링되고, CreateUser도 리렌더링되는 것을 볼 수 있다.

deps 배열에 users가 들어있어서 users가 변할 때마다 onCreate, onToggle, onRemove 함수가 새로 만들어지기 때문이다.

이것을 최적화하기 위해서는 deps에서 users를 지우고, 함수들에서 현재 useState로 관리하는 users를 참조하지 않게 하면 된다.

함수형 업데이트를 하게 되면, setUsers에 등록하는 콜백함수의 파라미터에서는 최신 users를 참조할 수 있기 때문에

굳이 deps에 users를 넣지 않아도 된다.

```
import React, { useRef, useState, useMemo, useCallback } from 'react';
import UserList from './UserList';
import CreateUser from './CreateUser';

function countActiveUsers(users) {
  console.log('활성 사용자 수를 세는중...');
  return users.filter(user => user.active).length;
}

function App() {
  const [inputs, setInputs] = useState({
    username: '',
    email: ''
  });

  const { username, email } = inputs;

  const onChange = useCallback(
    e => {
      const { name, value } = e.target;
      setInputs(inputs => ({
        ...inputs,
        [name]: value
      }));
    },
    []
  );

  const [users, setUsers] = useState([
    {
      id: 1,
      username: 'velopert',
      email: 'public.velopert@gmail.com',
      active: true
    },
    {
      id: 2,
      username: 'tester',
      email: 'tester@example.com',
      active: false
    },
    {
      id: 3,
      username: 'liz',
      email: 'liz@example.com',
      active: false
    }
  ]);

  const nextId = useRef(4);

  const onCreate = useCallback(() => {
    const user = {
      id: nextId.current,
      username,
      email
    };
    setUsers(users => users.concat(user));

    setInputs({
      username: '',
      email: ''
    });
    nextId.current += 1;
  }, [username, email]);

  const onRemove = useCallback(
    id => {
      // user.id 가 파라미터로 일치하지 않는 원소만 추출해서 새로운 배열을 만듬
      // = user.id 가 id 인 것을 제거함
      setUsers(users => users.filter(user => user.id !== id));
    },
    []
  );

  const onToggle = useCallback(
    id => {
      setUsers(users =>
        users.map(user =>
          user.id === id ? { ...user, active: !user.active } : user
        )
      );
    },
    []
  );

  const count = useMemo(() => countActiveUsers(users), [users]);

  return (
    <>
      <CreateUser
        username={username}
        email={email}
        onChange={onChange}
        onCreate={onCreate}
      />
      <UserList users={users} onRemove={onRemove} onToggle={onToggle} />
      <div>활성사용자 수 : {count}</div>
    </>
  );
}

export default App;
```

이렇게 해주면, 특정 항목을 수정하게 될 때 해당 항목만 리렌더링된다.

## 정리

useCallback, useMemo, React.memo는 컴포넌트의 성능을 실제로 개선할 수 있는 상황에서만 사용해야 한다.

예를 들어 User 컴포넌트에 b와 button에 onClick으로 설정해준 함수들은

useCallback을 사용한다고 해서 리렌더링을 방지할 수 있는 것이 아니기 때문에 굳이 사용할 필요가 없다.

또한, 렌더링을 최적화하지 않는 컴포넌트에 React.memo를 사용하면 오히려 불필요한 props를 비교하는 과정이 추가되는 것이기 때문에

실제로 렌더링을 방지할 수 있는 상황에서만 사용해야 한다.
