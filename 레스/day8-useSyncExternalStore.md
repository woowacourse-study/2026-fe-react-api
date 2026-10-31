## useSyncExternalStore

### 나온 배경

React 밖에서 사는 상태(외부 스토어)를 컴포넌트에서 구독하는 Hook.

React 18 이전에는 외부 스토어 구독을 보통 이렇게 짰다.

```jsx
const [state, setState] = useState(store.getState());
useEffect(() => store.subscribe(() => setState(store.getState())), []);
```

동시성 렌더링이 들어오면서 이 패턴에 구멍이 생겼다. 렌더링이 중단됐다 재개될 수 있게 되자, 렌더링 도중 스토어 값이 바뀌면 같은 화면 안에서 컴포넌트 A는 옛 값을, B는 새 값을 읽는 일이 가능해졌다. 이걸 tearing이라 부른다. React가 관리하는 상태(useState)는 React가 렌더링 단위로 버전을 잡아주니 안전하지만, 외부 스토어는 React가 모르는 사이에 바뀐다. 그 간극을 메우려고 나온 게 useSyncExternalStore라고 이해하면 좋다.

### 요약

```jsx
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)
```

- `subscribe(listener)`: 스토어에 listener를 등록하고 해제 함수를 반환해야 한다. 스토어가 바뀔 때마다 listener를 호출하면 React가 다시 렌더링한다
- `getSnapshot()`: 스토어의 현재 값을 반환한다. 값이 안 바뀌었으면 같은 참조를 반환해야 한다 (`Object.is` 비교)
- `getServerSnapshot()`: SSR/hydration 때 쓸 초기 스냅샷. 서버 렌더링을 안 하면 생략
- 이름의 Sync가 핵심이다. 외부 스토어 변경은 항상 동기적으로 화면에 반영된다. tearing을 막는 대신 그 업데이트에 한해 동시성 기능을 포기하는 트레이드오프다

Redux, Zustand 같은 상태 라이브러리가 내부에서 이걸로 React에 연결하고, `navigator.onLine`이나 `window.matchMedia`처럼 React 밖 브라우저 API를 구독할 때도 쓴다.

### 예시 — 미니 react-query 만들기

shopping-cart 프로젝트에서 react-query를 직접 구현하면서 썼다. 쿼리 캐시는 React 밖에 사는 평범한 클래스다.

```ts
export class QueryCache {
  private entries = new Map<string, QueryEntry>();

  getState<T>(key: QueryKey): QueryState<T> | undefined {
    return this.entries.get(hashKey(key))?.state as QueryState<T> | undefined;
  }

  subscribe(key: QueryKey, listener: Listener): () => void {
    const entry = this.ensureEntry(key);
    entry.listeners.add(listener);
    return () => entry.listeners.delete(listener);
  }

  private transition(entry: QueryEntry, state: QueryState<unknown>): void {
    entry.state = state; // 항상 새 객체로 교체
    entry.listeners.forEach((listener) => listener());
  }
}
```

이 캐시를 useQuery에서 useSyncExternalStore로 React에 연결한다.

```ts
const EMPTY: QueryState<unknown> = { status: "pending" };

export function useQuery<T>({ queryKey, queryFn }: UseQueryOptions<T>): UseQueryResult<T> {
  const cache = useQueryCache();

  const state = useSyncExternalStore(
    function subscribe(listener) {
      return cache.subscribe(queryKey, listener);
    },
    function getSnapshot() {
      return cache.getState<T>(queryKey) ?? (EMPTY as QueryState<T>);
    },
  );
  // ...
}
```

같은 queryKey를 구독하는 컴포넌트가 몇 개든, fetch가 끝나 `transition`이 listener들을 호출하면 전부 같은 스냅샷을 보고 다시 렌더링된다. 캐시 로직에는 React가 한 줄도 안 들어가서 테스트도 순수 클래스 단위로 끝난다.

### getSnapshot이 매번 새 객체를 반환하면

위 코드에서 `EMPTY`가 모듈 스코프 상수인 게 우연이 아니다.

```ts
// 이렇게 쓰면 무한 루프
return cache.getState<T>(queryKey) ?? { status: "pending" };
```

React는 렌더링 후 getSnapshot을 다시 불러 값이 바뀌었는지 `Object.is`로 확인하는데, 매번 새 객체를 반환하면 "스토어가 계속 바뀐다"고 판단해 다시 렌더링한다. 그게 또 새 객체를 만들고, 무한 루프다. React가 친절하게 "The result of getSnapshot should be cached" 에러를 던져주긴 한다.

캐시 쪽 `transition`이 상태를 항상 새 객체로 갈아끼우는 것도 같은 이유다. 스냅샷의 참조가 곧 "바뀌었는가"의 판단 기준이라서, 안 바뀌면 같은 참조·바뀌면 다른 참조라는 규약을 스토어가 지켜줘야 한다.

### 주의사항

- subscribe 함수의 참조가 바뀌면 React는 재구독(해제 후 다시 등록)한다. 위 예시처럼 컴포넌트 안에 인라인으로 선언하면 매 렌더링마다 재구독이 일어난다. 동작은 정확하지만 낭비이니, 잦은 렌더링이 문제되면 useCallback으로 참조를 고정하거나 컴포넌트 밖으로 뺀다.
- 외부 스토어 업데이트는 non-blocking 업데이트로 만들 수 없다. day4의 useTransition, day5의 useDeferredValue로 감싸도 해당 업데이트는 동기로 처리된다. 동시성 기능과 온전히 결합하려면 상태가 useState 안에 살아야 한다.
- 그래서 문서도 못 박는다: 가능하면 React 내장 상태를 써라. 이 훅은 이미 React 밖에 존재하는 것(외부 라이브러리, 브라우저 API)을 연결하는 용도지, 새 상태를 밖에 두는 근거가 아니다.
- SSR에서는 getServerSnapshot이 없으면 에러다. 서버와 클라이언트 첫 렌더링이 같은 값을 반환해야 hydration이 안 깨진다.
- day7의 useDebugValue가 커스텀 훅 만드는 사람을 위한 도구였다면, 이건 상태 라이브러리 만드는 사람을 위한 도구에 가깝다. 애플리케이션 코드에서 직접 볼 일은 드물고, 대부분 useQuery 같은 커스텀 훅 안에 숨는다.
