## useDeferredValue

### 나온 배경

React 18부터 정식 API

useTransition과 같은 목적(무거운 렌더링 때문에 화면이 버벅이는 걸 막는 것)인데, **내가 상태 업데이트를 직접 감쌀 수 없을 때** 쓴다.

useTransition은 `startTransition(() => setState(...))`처럼 업데이트를 감싼다. 
그런데 값이 props로 내려오거나 외부에서 정해져서 그 `setState`에 손댈 수 없는 경우가 있다. 이때 값 자체를, 늦게 따라오는 버전으로 만들어 쓰는 게 useDeferredValue이다.

### 요약

```jsx
const deferredValue = useDeferredValue(value);
```

- 원래 `value`가 바뀌면 화면은 일단 **예전 값**으로 먼저 그려지고, 여유가 생기면 `deferredValue`가 최신으로 따라옴
- 급한 업데이트(타이핑 등)가 진행 중이면 뒤로 미뤄지고, 끝나면 반영됨
- 이 지연된 값을 무거운 컴포넌트에 넘겨서 렌더링 부담을 미룬다

### 예시

```jsx
function SearchPage() {
  const [query, setQuery] = useState("");
  const deferredQuery = useDeferredValue(query);

  return (
    <>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <SearchResults query={deferredQuery} />
    </>
  );
}
```

입력창은 `query`로 즉시 반영되고, 무거운 `SearchResults`는 `deferredQuery`를 받아 한 박자 늦게 따라온다. 타이핑 중엔 목록이 예전 결과를 보여주다가, 입력이 잠잠해지면 최신으로 갱신된다.

### useTransition과 차이

- useTransition: **업데이트를 일으키는 쪽**에서 `startTransition`으로 감쌈. 내가 `setState`를 제어할 때.
- useDeferredValue: **값을 받는 쪽**에서 지연 버전을 만듦. 값을 제어 못 하고 받기만 할 때(props 등).

### 주의사항

- 값을 늦추는 것이지 렌더링 자체를 빠르게 만들진 않는다. 무거운 컴포넌트는 `memo`로 감싸야 지연 효과가 산다.
- 지연된 값과 최신 값이 잠시 어긋나는 구간이 생긴다. "예전 결과 흐리게 처리" 같은 UI로 표시하면 좋다.
