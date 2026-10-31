# useEffectEvent

<br />

## 1. 왜 등장했을까?

`useEffect`에서는 Effect 안에서 사용하는 모든 반응형 값을 의존성 배열에 포함해야 한다.

예를 들어 채팅방의 `roomId`가 변경되면, 기존 연결을 끊고 새로운 채팅방에 다시 연결해야 한다. 이처럼 **값의 변경에 따라 Effect를 다시 실행해야 하는 경우**에는 의존성 배열에 포함해야 한다.

하지만 모든 값이 Effect를 다시 실행해야 하는 것은 아니다.

예를 들어 `setInterval`은 한 번만 생성하면 되지만, 콜백에서는 항상 최신 `state`나 `props`를 읽고 싶을 수 있다. 이 경우에는 Effect를 다시 실행하기보다 **최신 값만 읽는 것**이 목적이다.

이처럼 **Effect는 다시 실행할 필요가 없지만 최신 값은 읽어야 하는 경우**를 해결하기 위해 `useEffectEvent`가 등장했다.

<br />

## 2. 어떤 훅일까?

```tsx
const onTick = useEffectEvent(callback);
```

`useEffectEvent`는 **Effect 내부에서 사용할 비반응형 로직을 분리하기 위한 Hook**이다.

반환된 함수는 항상 최신 `props`와 `state`를 참조하지만, Effect의 의존성이 되지는 않는다.

```tsx
const onTick = useEffectEvent(() => {
  setCount(count + increment);
});

useEffect(() => {
  const id = setInterval(onTick, 1000);

  return () => clearInterval(id);
}, []);
```

### 매개변수

- **callback** : Effect 내부에서 실행할 비반응형 로직

### 반환값

- **Effect Event 함수**

<br />

## 3. 어떤 상황에서 사용할까?

`useEffectEvent`는 **최신 값은 읽어야 하지만 Effect를 다시 실행할 필요는 없는 경우** 사용한다.

- `setInterval`의 콜백에서 최신 state 읽기
- 이벤트 리스너에서 최신 props/state 사용하기
- WebSocket, 채팅 등 연결은 유지하면서 최신 값만 참조하기

즉, Effect를 다시 실행해야 하는 로직은 `useEffect`에 두고,
최신 값만 읽으면 되는 로직은 `useEffectEvent`로 분리하고 싶을 때 사용한다.

React에서는 이를 각각 **반응형(reactive) 로직**과 **비반응형(non-reactive) 로직**이라고 표현한다.

<br />

## 4. 사용할 때 주의할 점

### 1. 의존성을 제거하기 위한 용도로 사용하지 않는다.

`useEffectEvent`는 Effect가 다시 실행되어야 하는 값을 숨기기 위한 Hook이 아니다.

Effect를 다시 실행해야 하는 값은 여전히 의존성 배열에 포함해야 한다.

### 2. Effect 내부에서만 호출한다.

Effect Event는 `useEffect`, `useLayoutEffect`, `useInsertionEffect` 내부에서만 호출해야 한다.

이벤트 핸들러나 일반 함수에서는 호출하지 않는다.

단 Effect의 의존성에는 추가되지 않는다.

<br />

## 한 줄 요약

> **useEffectEvent는 Effect의 비반응형 로직을 분리하여 최신 값을 읽으면서도 Effect의 재실행을 방지하기 위한 Hook이다.**
