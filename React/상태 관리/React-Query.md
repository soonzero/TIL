# React-Query

요즘 채용 공고를 보면서 느끼는 것은 react-query를 사용하는 기업들이 생각보다 많다는 것이다.

처음에 봤을 때에는 그냥 '저런 것도 있구나' 하고 넘어갔었는데, 하도 많이 보니까 뭔지 궁금해져서 찾아보니 상태 관리 라이브러리라고 한다.

아직 초짜인 나도 지금만 해도 알고 있는 상태 관리 관련된 기술이 좀 있는데 확실히 프론트엔드는 새로운 기술들이 많이 쏟아져 나온다는 게 실감이 된다.

특정 기술이나 라이브러리가 절대적으로 가장 좋다고 할 수는 없지만, 그 기술이 탄생하게 된 데에는 마땅한 이유가 있겠지 싶다.

그리고 라이브러리를 다양하게 알고 있어야 특정 상황에서 사용하기에 적절한 기술을 선택할 수 있기 때문에,

내가 당장 사용하지 않는다 해도 알고 있는 게 좋다는 생각이 들었다. 그래서 오늘은 React-Query에 대해 알아보려고 한다.

<hr />

React-Query는 전역 상태 관리 라이브러리라기보다, 서버와 클라이언트 간의 비동기 작업을 쉽게 다룰 수 있도록 도와주는 라이브러리다.

프론트엔드에서 가장 어려운 부분 중 하나가 비동기 작업을 통해 서버의 상태를 가져오고 업데이트하는 것과 같은 관리 부분인데,

이 부분을 쉽게 해주는 라이브러리라고 생각하면 쉬울 것 같다.

## 왜 굳이 React-Query일까?

사실 redux 같은 전역 상태 관리 라이브러리로도 충분할텐데 왜 굳이 React-Query를 사용하는지 궁금했다.

이유를 찾아보면서 가장 크게 와닿았던 이유 중 하나는 바로 이거다.

    SNS 서비스 게시판

    1. 게시판 접속 시 글 리스트를 받아오는 코드를 실행한다.

    2. 사용자가 새로운 게시글을 등록하면, 서버에 새로운 데이터를 추가하는 코드를 실행한다.

    3. 글 리스트를 최신 상태로 업데이트하기 위해 서버로부터 게시판 글 리스트를 다시 받아오는 코드를 실행해야 한다.

    +. 다른 사용자가 새로운 게시글을 등록하는 경우도 있기 때문에 주기적으로 새로운 글 리스트를 받아와야 한다.

    이 기능들을 구현하기 위해서 수많은 코드를 작성해야 하고, 최적화도 해야 하고.. 비용이 크다고 해야하나?

위처럼 전역 상태 관리 라이브러리들이 클라이언트의 상태에서는 잘 동작하지만 서버 상태에 대해서는 그렇지 못하기 때문에,

React-Query와 같은 서버 상태 관리 라이브러리가 나타나게 되었다고 한다.

### 서버 상태의 특징

[공식 문서](https://react-query.tanstack.com/overview)에 따르면 서버 상태는 다음과 같은 특징을 가진다고 한다.

1. 보통 앱 내에 속하지 않고, 원격 저장소에 저장되어 있기 때문에 제어할 수 없다.

2. 데이터를 가져오거나 업데이트하기 위해서는 비동기 API가 필요하다.

3. 나만 사용하는 것이 아니라, 다른 사람들과 함께 사용하기 때문에 언제 어떻게 update될 지 모른다.

4. 따라서 내가 당장 사용하는 앱 데이터가 outdated 상태가 될 수 있다.

- 클라이언트는 서버 데이터의 snapshot을 사용하기 때문에, 클라이언트에서 보이는 서버 데이터가 최신이라는 보장이 없다.

서버 상태는 내가 직접 관리할 수 있는 것이 아니기 때문에 완성도 높은 앱을 만들기 위해서는

데이터의 만료에 따른 업데이트나 캐싱, 서버 데이터를 호출하는 과정 등에 각별한 주의를 기울여야 한다.

### React-Query의 장점

React-Query는 아래와 같이 프론트엔드 개발자가 구현하기 귀찮은 일들을 간단하게 구현할 수 있도록 해준다.

- 캐싱 + 데이터를 캐시에서 유지할 시간 설정 가능

- get을 한 데이터를 update하면 자동으로 get을 다시 수행

- API 데이터의 만료 시간 설정 가능

- 동일 데이터를 여러 번 요청하는 경우 한 번만 요청

- 브라우저 focus할 때 리프레시

- 리프레시 간격

- 비동기 과정 선언적으로 관리 가능(데이터 불러오기 성공, 로딩, 에러 등)

- React Hook과 사용하는 구조가 비슷함

* 원격 데이터를 생성하거나 업데이트, 삭제 하는 등의 기능

## React-Query

### 기본

Query는 4개의 상태를 가지며, useQuery가 반환하는 객체의 프로퍼티로 어떤 상태인지 확인이 가능하다.

1. `fresh`: 새롭게 추가된 쿼리 인스턴스로, active 상태의 시작

   - 기본 staleTime이 0이기 때문에 아무것도 설정해주지 않으면 호출이 끝나고 바로 `stale` 상태로 변한다.

   - staleTime을 늘려줄 경우 그 시간동안 fresh한 상태가 유지되는데, 이때는 쿼리가 다시 마운트되더라도 fetching이 발생하지 않고 기존의 fresh한 값을 반환한다.

2. `fetching`: 요청을 수행하는 중인 쿼리

3. `stale`: 인스턴스가 존재하지만 이미 fetching이 완료된 쿼리

   - 특정 쿼리가 stale된 상태에서 같은 쿼리 마운트를 시도하면, 캐싱된 데이터를 반환하고 re-fetching을 시도한다.

4. `inactive`: active 인스턴스가 하나도 없는 쿼리

   - inactive된 이후에도 cacheTime 동안 캐시된 데이터가 유지되지만, cachTime이 지나면 GC\*된다.

   - [GC(Garbage Collection)](https://github.com/soonzero/TIL/blob/main/JS/가비지%20컬렉션.md): 쓸모 없어진 객체가 차지하는 메모리를 자동으로 해제하는 것

컴포넌트가 리렌더링 되면서 새로운 쿼리가 만들어지고, 이전의 렌더링에서 호출됐던 쿼리들은 inactive 상태가 된다.

렌더링 간에 다시 호출되지 않고 언마운트 되는 쿼리들이 inactive가 되는 듯하다.

다음 네 가지 경우에 re-fetching이 발생한다.

1. 런타임에 stale인 특정 쿼리 인스턴스가 다시 만들어졌을 때

2. window가 다시 포커스되었을 때(옵션으로 on/off 가능)

3. 네트워크가 다시 연결되었을 때(옵션으로 on/off 가능)

4. refetch interval이 있을 때

   - 요청 실패한 쿼리는 3번 더 백그라운드 단에서 요청하며, `retry`/`retryDelay` 옵션으로 간격과 횟수를 커스텀할 수 있다.

### Queries

```
const { status, data, error, isFetching, isPreviousData } = useQuery(
	["projects", page],
	() => fetchProjects(page),
	{ keepPreviousData: true, staleTime: 5000 }
);

// 예외 처리는 reject가 아닌 throw Error을 이용
const { error } = useQuery(
	["todos", todoId],
	async () => {
		if (somethingGoesWrong) {
			throw new Error("Error!")
		}

		return data;
	});
```

- 쿼리는 server state를 요청하는 프로미스를 리턴하는 함수와 함께 unique key로 맵핑된다.

- 쿼리는 콜백 함수의 요청이 프로미스를 반환한다면 일단 잘 작동하지만, 서버의 데이터를 바꾸는 요청이라면 `useMutation` 사용이 권장된다.

- `useQuery` 훅은 2개의 인자를 받는다.

  - 쿼리의 unique key

  - 프로미스를 반환하는 함수(이 함수는 반드시 resolve Promise를 반환하거나 에러를 throw해야 한다.

- unique key

  - re-fetching, caching 등을 할 때 참조되는 값으로 한 번 fresh가 되었다면 계속해서 추적이 가능하다.

  - 주로 배열을 사용하며, 배열의 요소로 쿼리의 이름을 나타내는 문자열과 프로미스를 리턴하는 함수의 인자로 쓰이는 값을 넣는다.

- `useQuery`의 반환값: 객체, 요청의 상태를 나타내는 몇 가지 프로퍼티, 요청의 결과나 에러값을 갖는 프로퍼티도 포함된다.

  - isIdle, isLoading, isError, isSuccess, status

  - error, data, isFetching: 런타임 동안 요청이 한 번 이상 발생했다면 무조건 값이 존재한다.

- 쿼리 요청 함수의 상태를 표현하는 status, is\_\_\_\_ 프로퍼티는 각각 문자열, 불리언 값으로 해당 상태를 식별할 수 있다.

  - idle: 쿼리 data가 하나도 없고 비어있을 때로, `{ enabled: false }` 상태로 쿼리가 호출되었을 때 이 상태로 시작된다.

  - loading: 로딩 중일 때

  - error: 에러가 발생했을 때

  - success: 요청에 성공했을 때

- 주요 쿼리 옵션

  - enabled: `true`로 설정할 시 자동으로 쿼리의 요청 함수가 호출되는 일이 없다.

  - keepPreviousData: success와 loading 사이의 널뛰기를 방지한다.

  - placeholderData: mock 데이터 설정도 가능하지만, 캐싱이 불가능하다.

  - initialData: 초기값 설정

  - 쿼리에 여러가지 옵션을 설정해서 데이터를 관리할 수 있다.

### Query Keys

```
useQuery(["todo", 5, { preview: true }], ...)
// queryKey === ["todo", 5, { preview: true }]
```

- 문자열: 구별되는 문자열로 키를 줄 수 있으며, 인자가 하나인 배열로 convert 된다.

- 배열: 문자열과 함께 숫자를 주면, 같은 문자열로 같은 key를 사용하면서도 id로도 구별이 가능하다.

- 콜백함수에 주는 인자: 배열의 마지막 요소이며, 역시 쿼리를 구별하는 데 쓰이고, 엔드포인트가 같더라도 요청에 넣는 body나 query params가 다르면 다른 쿼리 인스턴스로 취급된다.

- 내용이 같더라도 순서에 따라 다르게 해싱되기 때문에, 배열 요소의 순서도 중요하다.

- 요청 함수가 특정 변수에 의존할 때, 쿼리 키 배열에 객체로 같이 넣어주면 요청 함수 내에서 인자로 객체를 받아 이용할 수 있다.

```
function Todos({ todoId }) {
	const result = useQuery(["todos", todoId], () => fetchTodoById(todoId))
}

function Todos({ status, page }) {
	const result = useQuery(["todos", { status, page }], fetchTodoList)
}

// 쿼리 요청 함수에서 queryKey에 접근할 수 있다.
function fetchTodoList({ queryKey }) {
	const [_key, { status, page }] = queryKey
	return new Promise()
}
```

### Parallel

쿼리가 여러 개 선언되어 있는 일반적인 상황일 경우, 쿼리 함수들은 그냥 병렬로 요청되어 처리되므로 쿼리 처리의 동시성을 극대화시킨다.

```
function App() {
	// 아래 쿼리 함수들은 병렬로 처리된다.
	const usersQuery = useQuery("users", fetchUsers)
	const teamsQuery = useQuery("teams", fetchTeams)
	const projectsQuery = useQuery("projects", fetchProjects)
	...
}
```

렌더링이 거듭되는 사이에 계속 쿼리가 수행되어야 한다면 쿼리를 수행하는 로직이 hook 룰에 위배될 수 있다.

이럴 때 사용할 수 있는 것이 바로 `useQueries`이다.

```
function App({ users }) {
	const userQueries = useQueries(
		users.map(user => {
			return {
				queryKey: ["user", user.id],
				queryFunc: () => fetchUserById(user.id),
			}
		})
	)
}
```

### Query Retries

```
import { userQuery } from "react-query"

// 재호출 횟수를 옵션으로 커스터마이징할 수 있다.
const result = useQuery(["todos", 1], fetchTodoListPage, {
	retry: 10, // 에러를 display할 때까지 10번을 더 호출한다.
})
```

- `useQuery` 호출로 요청에 실패하는 경우, 최대 연속 요청 한계까지 계속 요청한다. 기본값은 3으로 설정되어 있다.

- retry 옵션으로 쿼리의 재요청 횟수를 정한다.

- retryDelay 옵션을 설정하면 쵸엉이 한 번 실패했을 때, 설정한 일정 시간 이후 다시 요청한다.

### useMutation

```
function App() {
	const mutation = useMutation(newTodo => axios.post("/todos", newTodo))

	return (
		<div>
			{mutation.isLoading ? (
				"todo list 로딩 중..."
			) : (
				<>
					{mutation.isError ? (
						<div>에러 발생: {mutation.error.message}</div>
					) : null}
					{mutation.isSuccess ? <div>todo 추가!</div> : null}

					<button onClick={() => {
						mutation.mutate({ id: new Date(), title: "청소기 밀기" })
					}}
					>
						Todo 생성
					</button>
				</>
			)}
		</div>
	)
}
```

- useQuery와는 다르게 Create, Update, Delete 하며 서버의 state에 사이드 이펙트를 일으키는 경우에 사용한다.

- useMutation으로 mutation 객체를 정의하고 mutate 메소드를 사용하면, 요청 함수를 호출해 요청이 보내진다.

- useMutation이 반환하는 객체 프로퍼티로 제공되는 상태는 useQuery와 동일하다.

- `mutation.reset`: 현재의 error와 data를 모두 지울 수 있다.

- 두 번째 인자로 콜백 객체를 넘겨줘서 라이프사이클 인터셉트 로직을 짤 수 있다.

```
useMutation(addTodo, {
	onMutate: variables => {
		// mutation 시작
		// onMutate가 반환하는 객체는 이하 생명주기에서 context 파라미터로 참조할 수 있다.
		return { id: 1 }
	},
	onError: (error, variables, context) => {
		// 에러 발생
		console.log(`롤백: ${context.id}`)
	},
	onSuccess: (data, variables, context) => {
		// 성공
	},
	onSettled: (data, error, variables, context) => {
		// 성공, 에러 여부 관계 없이 끝났을 때
	}
})
```

- useQuery를 사용할 때처럼 실패 시 retry가 기본값은 아니지만, retry 옵션을 추가할 수는 있다.

### Invalidation

Invalidation은 stale 상태의 쿼리를 폐기하는 것을 의미한다.

쿼리의 데이터가 요청을 통해 서버에서 변경되었다면, 백그라운드에 남아있는 데이터는 앱에서 쓸모없어질 수 있다.

`invalidateQueries` 메소드를 사용하면 개발자가 직접 query가 stale되는 지점을 설정할 수 있다.

해당 메소드가 호출되면 쿼리가 바로 stale 상태로 변경되고, re-fetching이 진행된다.

쿼리에 특정 키가 공통적으로 들어가 있는 경우, 메소드에 전달하는 인자를 사용해 한 번에 폐기할 수 있다.

```
// 캐시가 존재하는 모든 쿼리들을 폐기한다.
queryClient.invalidateQueries()

// "todos"로 시작하는 모든 쿼리들을 폐기한다.
queryClient.invalidateQueries("todos")

queryClient.invalidateQueries({
	predicate: query => query.queryKey[0] === "todos" && query.queryKey[1]?.version >= 10,
})
```

mutation이 성공하는 경우, 데이터가 변경되었으므로 해당 데이터를 re-fetching 해야 한다.

따라서 관련 query도 폐기되어야 하는데, 이럴 때에는 mutation 생명주기 콜백 안에서 폐기해주는 것이 자연스럽다.

```
import { useMutation, useQueryClient } from "react-query";

const queryClient = useQueryClient()

// mutation 성공 시, 쿼리의 데이터를 폐기해서 관련된 쿼리가 re-fecth되도록 한다.
const mutation = useMutation(addTodo, {
	onSuccess: () => {
		queryClient.invalidateQueries("todos");
		queryClient.invalidaateQueries("reminders")
	}
})
```

mutation 요청 후 서버에서 받는 response가 새로 갱신된 데이터일 경우,

mutation을 성공했을 때 쿼리 데이터를 바꿔주는 queryClient 인스턴스의 `setQueryData` 메소드를 사용하면 좋다.

```
const queryClient = useQueryClient()

const mutation = useMutation(editTodo, {
	onSuccess: data => queryClient.setQueryData(["todo", { id: 5 }], data)
})

mutation.mutate({
	id: 5,
	name: "설거지하기",
})

// mutation의 response 값으로 업데이트된 data를 사용할 수 있다.
const { status, data, error } = useQuery(["todo", { id: 5 }], fetchTodoById)
```

### Caching

1. useQuery의 첫 번째 or 새로운 인스턴스 마운트

   - 만약에 런타임 간 최초로 fresh 쿼리가 호출되었다면 캐싱한다.

   - fetching이 끝나면 설정한 staleTime이 지난 후 해당 쿼리를 stale로 변경한다.

2. 앱 어딘가에서 useQuery 두 번째 인스턴스 마운트

   - 이미 쿼리가 stale 상태이므로 이전 요청 때 만들어뒀던 캐시는 반환한다.

   - re-fetching하면서 캐시를 새로 업데이트한다.

3. 쿼리가 언마운트되거나 더 이상 사용하지 않을 때

   - 마지막 인스턴스가 언마운트되어 inactive 상태가 됐을 때 cacheTime으로 설정해둔 시간이 지나면 자동으로 삭제한다.
