## useDeferredValue

공식 문서의 표현

> `useDeferredValue`는 일부 UI 업데이트를 지연시킬 수 있는 React Hook입니다.

### 코드

```ts
const deferredValue = useDeferredValue(value);
```

#### 매개변수

`value`: 모든 타입을 가질 수 있는, 지연시키려는 값

#### 반환값

`currentValue`: 초기에는 value값이고, 업데이트가 발생하면 렌더링하는 지연된 값

### 언제 쓰나?

업데이트 시, 로딩 중에 이전 값을 보여준다.

자세히 말해서, `useDeferredValue`를 호출하여 UI 일부 업데이트를 지연할 수 있다.

```ts
function App() {
  const [query, setQuery] = useState('');
  return (
    <>
      <label>
        Search albums:
        <input value={query} onChange={e => setQuery(e.target.value)} />
      </label>
      <Suspense fallback={<h2>Loading...</h2>}>
        <SearchResults query={query} />
      </Suspense>
    </>
  );
```

위 코드는 검색 결과를 보여주는 코드이다. `Suspense`에 의해 검색어가 변경되면 로딩 폴백을 렌더링한다.

검색어가 변경되면 결과를 유지하지 못한 채 무조건 로딩 폴백을 렌더링하게 된다.

```ts
export default function App() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);
  return (
    <>
      <label>
        Search albums:
        <input value={query} onChange={(e) => setQuery(e.target.value)} />
      </label>
      <Suspense fallback={<h2>Loading...</h2>}>
        <SearchResults query={deferredQuery} />
      </Suspense>
    </>
  );
}
```

그래서 `useDeferredValue` 훅을 사용한 `deferredQuery`를 검색결과에 전달해서(지연된 값을 전달) 새로운 결과가 준비될 때까지 이전 결과를 계속 표시한다.

조금 더 자세히 말하면, `<input />`에 전달되는 query는 즉시 변경되지만, `deferredQuery`는 데이터가 로딩될 때까지 이전 값을 유지하므로 `SearchResults`는 잠시 동안 오래된 결과를 표시한다.

위 예시에서는 최신 쿼리에 대한 결과 목록이 아직 로딩 중이라는 표시가 없어서, 새 결과를 로딩하는 데 시간이 오래 걸리는 경우 사용자에게 혼란을 줄 수 있다.

```ts
<div
  style={{
    opacity: query !== deferredQuery ? 0.5 : 1,
  }}
>
  <SearchResults query={deferredQuery} />
</div>
```

그래서 위 코드처럼 시각적인 효과를 줄 수도 있다!

### 결론

사용자에게 보여지는 정보에 대해서, UX를 개선해주는 훅이다.

어떤 조건이나 입력이 변경될 때마다 로딩 폴백을 보여주는 것은 사용자에게 답답함을 줄 수 있기 때문에, 의도적으로 지연된 값을 미리 보여줌으로써 UX를 개선시킨다.

그래도 지연된 값을 보여주는 것에 혼란이 올 수 있기 때문에, 투명도와 같은 효과를 사용자에게 보여줘서 혼란을 방지할 수 있다.
