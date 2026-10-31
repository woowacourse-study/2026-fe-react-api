## useTransition

### 나온 배경

"이건 급한 일 아니니까 화면 안 멈추게 처리해줘"라고 React에게 알리는 Hook. React 18부터 정식 API.

검색창 입력을 예로 들면,

- 입력한 글자가 검색창에 바로 보이는 것 → 급한 일
- 그 검색어로 무거운 결과 목록을 다시 그리는 것 → 덜 급한 일

React는 기본적으로 모든 업데이트를 급한 일로 처리해서 우선순위를 못 나눈다. 이 둘이 한 업데이트로 묶이면 타이핑할 때마다 버벅인다.

### 요약

```jsx
const [isPending, startTransition] = useTransition();
```

- `startTransition(fn)` 안의 상태 업데이트는 **비긴급(transition)**으로 표시됨 → 중단 가능, 더 급한 업데이트에 양보
- `isPending` → 그 전환이 아직 처리 중인지 알려주는 boolean (로딩 표시용)

핵심은 "바로 느껴야 하는 값(그냥 setState)"과 "늦어도 되는 무거운 업데이트(startTransition)"를 나누는 것.

### 예시

```jsx
function SearchPage() {
  const [inputValue, setInputValue] = useState("");
  const [searchQuery, setSearchQuery] = useState("");
  const [isPending, startTransition] = useTransition();

  function handleChange(e) {
    const value = e.target.value;
    setInputValue(value);              // 급한 일: 입력창은 즉시 반영
    startTransition(() => {
      setSearchQuery(value);           // 덜 급한 일: 무거운 결과는 전환으로
    });
  }

  return (
    <>
      <input value={inputValue} onChange={handleChange} />
      {isPending && <p>검색 결과 바꾸는 중...</p>}
      <SearchResults query={searchQuery} />
    </>
  );
}
```

state를 둘로 나눈 게 포인트다. `inputValue`는 사용자가 실제로 보는 값이라 느리면 안 되고, `searchQuery`는 무거운 목록을 그리는 값이라 전환으로 미룬다.

### 주의사항

- 입력창 값 자체(`setInputValue`)를 `startTransition`으로 감싸면 안 된다. 즉시 반영돼야 하므로.
- 성능을 빠르게 만드는 게 아니라, 무거운 렌더링이 있어도 입력·클릭 같은 반응을 먼저 처리해 **버벅임을 줄이는** Hook이다.
