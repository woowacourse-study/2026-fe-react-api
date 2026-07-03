# useEffect

<br />

## 1. 왜 등장했을까?

React는 **현재 상태를 기반으로 UI를 계산하는 것**에 집중한다.
따라서 컴포넌트는 가능한 한 **순수 함수처럼 동작**하기를 기대한다.

하지만 실제 애플리케이션에서는 `fetch`, `localStorage`, `document.title`, `setInterval`처럼 React가 관리하지 않는 외부 시스템과도 상호작용해야 한다.

`useEffect`는 이러한 문제를 해결하기 위해 등장했다.

React는 `useEffect를 **탈출구**라고 표현하는데,

렌더링 과정과 외부 시스템과의 동기화를 분리하여,
컴포넌트는 순수하게 UI를 계산하고 `useEffect`는 렌더링 이후 외부 시스템과 동기화하는 역할을 담당한다.

<br />

## 2. 어떤 훅일까?

```tsx
useEffect(setup, dependencies?);
```

`useEffect`는 **React 컴포넌트를 외부 시스템과 동기화하기 위한 Hook**이다.

렌더링이 완료된 이후 `setup` 함수를 실행하며, 컴포넌트가 언마운트되면 `cleanup`함수를 실행한다.

의존성이 변경되면 이전 Effect를 정리(cleanup)한 뒤 새로운 Effect를 설정(setup)한다.

```tsx
useEffect(() => {
  const connection = createConnection(serverUrl, roomId);
  connection.connect();

  return () => {
    connection.disconnect();
  };
}, [serverUrl, roomId]);
```

### 매개변수

- **setup** : 외부 시스템과 동기화하는 함수. 필요한 경우 정리(cleanup) 함수를 반환할 수 있다.
- **dependencies?** : Effect가 다시 실행되어야 하는 값을 담은 의존성 배열.

### 반환값

- **반환값 없음 (`undefined`)**

<br />

## 3. 어떤 상황에서 사용할까?

`useEffect`는 **React가 관리하지 않는 외부 시스템과 동기화해야 할 때** 사용한다.

- API 요청 및 응답 처리 (`fetch`)
- 브라우저 API와 동기화 (`document.title`, `localStorage` 등)
- 타이머 등록 및 해제 (`setInterval`, `setTimeout`)
- 이벤트 리스너 등록 및 해제 (`addEventListener`)
- 외부 라이브러리나 서버와 연결 (`WebSocket`, 지도 라이브러리 등)

반대로 **렌더링 중 계산할 수 있는 값이나 이벤트에서 처리할 수 있는 로직에는 `useEffect`를 사용하지 않는 것이 좋다.**

<br />

## 4. 사용할 때 주의할 점

### 1. 외부 시스템과 동기화할 때만 `useEffect`를 사용한다.

`useEffect`는 React가 관리하지 않는 외부 시스템과 동기화하기 위한 Hook이다.

따라서 렌더링 중 계산할 수 있는 값이나 이벤트에서 처리할 수 있는 로직에는 `useEffect`를 사용할 필요가 없다.

### 2. 의존성은 Effect에서 사용하는 모든 반응형 값을 포함해야 한다.

Effect에서 사용하는 반응형 값은 모두 의존성 배열에 포함해야 한다.

객체나 함수는 렌더링마다 새로 생성될 수 있으므로, 불필요한 Effect 재실행을 막기 위해 가능하면 Effect 내부에서 생성하는 것이 좋다.

### 3. Cleanup은 이전 Effect를 정리하는 역할이다.

의존성이 변경되거나 컴포넌트가 언마운트되면 React는 이전 Effect를 먼저 정리(cleanup)한 뒤 새로운 Effect를 실행한다.

따라서 Effect에서는 **시작한 것을 정리하는** 대칭적인 구조를 유지하는 것이 좋다.

```tsx
useEffect(() => {
  const id = setInterval(tick, 1000);

  return () => clearInterval(id);
}, []);
```

### 4. Effect 안에서 state를 변경할 때는 주의한다.

`useEffect`는 기본적으로 **렌더링이 완료된 이후** 실행된다.

Effect 안에서 state를 변경하면 리렌더링이 발생한다.  
만약 변경된 state가 Effect의 의존성에 포함되어 있다면, Effect가 다시 실행되면서 반복이 발생할 수 있다.

```tsx
// ❌ Effect가 반복 실행될 수 있다.
useEffect(() => {
  setCount(count + 1);
}, [count]);
```

이처럼 Effect가 다시 렌더링의 원인이 되고, 그 렌더링이 다시 Effect 실행의 원인이 되는 구조는 주의해야 한다.

<br />

### 5. 비슷한 훅 비교

`useInsertionEffect`는 브라우저가 레이아웃을 계산하기 전에 CSS를 삽입하고,
`useLayoutEffect`는 화면이 그려지기 전에 DOM을 측정하거나 수정할 수 있다.

실행 흐름은 다음과 같다.

```text
React Render
  → DOM Commit
  → useInsertionEffect (CSS 삽입)
  → useLayoutEffect (DOM 측정 및 수정)
  → Browser Layout & Paint
  → useEffect (외부 시스템과 동기화)
```

<br />

## 한 줄 요약

> **useEffect는 React 컴포넌트를 외부 시스템과 동기화하기 위한 Hook이다.**
