# useState

<br />

## 1. 왜 등장했을까?

일반 변수는 값이 변경되어도 React가 이를 감지하지 못해 화면을 다시 렌더링하지 않는다. 또한 컴포넌트가 다시 렌더링되면 일반 변수는 초기화되어 이전 값을 유지할 수 없다.

동적인 UI를 만들기 위해서는 **렌더링 사이에도 값을 기억하고**, **값이 변경되면 React가 이를 인지하여 다시 렌더링할 수 있는 상태 관리 기능**이 필요했다. `useState`는 이러한 문제를 해결하기 위해 등장한 Hook이다.

<br />

## 2. 어떤 훅일까?

```tsx
const [state, setState] = useState(initialState);
```

`useState`는 컴포넌트 내부의 상태를 관리하는 Hook이다. 렌더링 사이에도 상태를 유지하며, 상태가 변경되면 React가 컴포넌트를 다시 렌더링한다.

### 매개변수

- **initialState** : 상태의 초기값

### 반환값

- **현재 상태(state)**
- **set 함수** : 상태를 다른 값으로 업데이트하고 리렌더링한다.

<br />

## 3. 어떤 상황에서 사용할까?

렌더링 이후에도 기억되어야 하고, 값이 변경될 때 화면에 반영되어야 하는 데이터를 관리할 때 사용한다.

예를 들어 다음과 같은 값들이 있다.

- 입력값
- 체크 여부
- 모달의 열림/닫힘
- 선택된 탭
- 서버에서 받아와 화면에서 관리하는 데이터

반대로 현재 `props`나 다른 `state`로 계산할 수 있는 값은 **파생값**으로 계산하는 것이 좋다.

<br />

## 4. 사용할 때 주의할 점

### 1. 초기화 함수와 업데이터 함수는 순수 함수여야 한다.

`initialState`와 `set` 함수에는 함수를 전달할 수 있다.

```tsx
const [todos] = useState(createInitialTodos);

setCount((prev) => prev + 1);
```

**초기화 함수와 업데이터 함수는 React가 실행을 제어하는 함수이므로 항상 순수 함수여야 한다.** React는 이 함수를 언제, 몇 번 실행할지 보장하지 않으며, 개발 환경에서는 순수성을 검증하기 위해 여러 번 호출할 수 있다.

### 2. set 함수는 현재 렌더링의 값을 변경하지 않는다.

`set` 함수는 현재 state를 즉시 변경하는 것이 아니라 **다음 렌더링에서 사용할 state를 예약**한다.

```tsx
setCount(count + 1);

console.log(count); // 이전 값
```

### 3. 여러 set 함수는 큐에 쌓여 한 번에 처리된다.

이벤트 핸들러 안에서 호출된 `set` 함수들은 큐에 저장되었다가 이벤트가 끝난 뒤 한 번에 처리된다(Batching).

이전 state를 기반으로 계산해야 한다면 업데이터 함수를 사용하는 것이 안전하다.

### 4. React는 Object.is로 state를 비교한다.

이전 state와 다음 state를 `Object.is`로 비교하여 같으면 렌더링을 건너뛴다.

배열이나 객체는 참조값을 비교하므로 직접 수정하지 말고 새로운 객체나 배열을 만들어 전달해야 한다.

### 5. key는 state를 유지할지 결정하는 기준이다.

React는 컴포넌트의 위치와 `key`를 기준으로 기존 state를 유지할지 새로운 state를 생성할지 판단한다.

- 같은 위치 + 같은 key → 기존 state 유지
- key 변경 → 새로운 컴포넌트로 판단하여 state 초기화

리스트에서는 `index`를 key로 사용하면 state가 다른 항목으로 이어질 수 있으므로 가능한 고유한 값을 사용한다.

### 6. 렌더링 중 set 함수 호출은 대부분 피한다.

일반적으로 state 업데이트는 이벤트 핸들러에서 수행한다.

다만 이전 props와 현재 props를 비교해야 하는 드문 경우에는 **조건부로 현재 컴포넌트에서만** 렌더링 중 `set` 함수를 호출할 수 있다.

(useEffect를 사용하는 것보다 좋은 방법이다.)

```tsx
export default function CountLabel({ count }) {
  const [prevCount, setPrevCount] = useState(count);
  const [trend, setTrend] = useState(null);
  if (prevCount !== count) {
    setPrevCount(count);
    setTrend(count > prevCount ? "increasing" : "decreasing");
  }
  return (
    <>
      <h1>{count}</h1>
      {trend && <p>The count is {trend}</p>}
    </>
  );
}
```

하지만 그 전에 아래 방법을 먼저 고려한다.

- 파생값으로 계산할 수 있는지 확인한다.
- 전체 state를 초기화해야 한다면 `key`를 변경한다.
- 이벤트에서 처리할 수 있다면 이벤트 핸들러에서 함께 업데이트한다.

<br />

## 한 줄 요약

> **useState는 렌더링 사이에도 값을 기억하고, 상태 변경을 React에 알려 화면을 다시 렌더링하기 위한 상태 관리 Hook이다.**
