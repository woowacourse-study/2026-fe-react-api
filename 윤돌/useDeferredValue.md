# useDeferredValue

<br />

## 1. 왜 등장했을까?

`useTransition`은 상태 업데이트의 우선순위를 낮추기 위한 Hook이다.

하지만 항상 상태를 변경하는 코드를 수정할 수 있는 것은 아니다.

컴포넌트가 부모로부터 전달받은 값을 사용하는 경우에는 `startTransition`을 사용할 수 없다.

`useDeferredValue`는 이러한 문제를 해결하기 위해 등장했다.

전달받은 값을 바로 사용하지 않고, 이전 값을 잠시 유지하여 무거운 UI 업데이트를 늦출 수 있다.

<br />

## 2. 어떤 훅일까?

```tsx
const deferredValue = useDeferredValue(value);
```

`useDeferredValue`는 전달받은 값의 반영을 늦추는 Hook이다.

React는 우선 최신 UI를 먼저 화면에 반영하고, 여유가 생기면 `deferredValue`를 최신 값으로 업데이트한다.

### 매개변수

- **value** : 반영을 늦출 값

### 반환값

- **deferredValue** : 잠시 이전 값을 유지하다가 나중에 최신 값을 따라가는 값

<br />

## 3. 어떤 상황에서 사용할까?

`useDeferredValue`는 **무거운 UI를 렌더링하는 컴포넌트가 부모로부터 값을 전달받는 경우** 사용한다.

- 검색 결과 리스트
- 대용량 리스트
- 복잡한 차트나 그래프

```tsx
const deferredQuery = useDeferredValue(query);

return <SearchList query={deferredQuery} />;
```

입력창은 최신 값을 즉시 반영하고, 검색 결과는 이전 값을 잠시 유지한 뒤 나중에 최신 값으로 갱신한다.

<br />

## 4. 사용할 때 주의할 점

`useDeferredValue`는 **값을 캐싱하는 Hook이 아니다.**

- 이전 값을 잠시 유지할 뿐, 결국 최신 값을 따라간다.
- UI가 잠시 오래된(stale) 값을 보여줄 수 있다.
- `query !== deferredQuery`를 이용해 업데이트 중임을 표시하면 사용자 경험을 개선할 수 있다.
- 상태를 변경하는 코드를 수정할 수 있다면 `useTransition`을 먼저 고려하는 것이 일반적이다.

<br />

## 한 줄 요약

> **useDeferredValue는 전달받은 값의 반영을 늦춰 무거운 UI가 사용자 입력을 방해하지 않도록 하는 Hook이다.**
