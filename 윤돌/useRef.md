# useRef

<br />

## 1. 왜 등장했을까?

React 컴포넌트에는 다양한 종류의 값이 존재한다.

- **상태(state)** 는 변경되면 화면이 함께 변경되어야 하므로 `useState`로 관리한다.
- **일반 변수** 는 렌더링될 때마다 다시 생성되어 이전 값을 기억하지 못한다.
- **전역 변수** 는 모든 컴포넌트가 공유하므로 컴포넌트마다 독립적인 값을 관리하기 어렵다.

하지만 **렌더링과는 무관하면서도, 렌더링 사이에 계속 기억해야 하는 값**도 존재한다.

`useRef`는 이러한 값을 저장하기 위해 등장한 Hook이다. 값이 변경되어도 리렌더링을 발생시키지 않으며,
같은 컴포넌트에서 렌더링이 반복되어도 동일한 값을 계속 참조할 수 있다.

<br />

## 2. 어떤 훅일까?

```tsx
const ref = useRef(initialValue);
```

`useRef`는 렌더링과 무관하게 값을 저장하고 참조하기 위한 Hook이다.
`ref.current`를 통해 값을 읽고 쓸 수 있으며,값이 변경되어도 React는 리렌더링하지 않는다.

```tsx
function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>Focus</button>
    </>
  );
}
```

### 매개변수

- **initialValue** : `ref.current`의 초기값

### 반환값

- **ref 객체** : `current` 프로퍼티를 가지며, 값을 저장하거나 참조할 수 있다.

<br />

## 3. 어떤 상황에서 사용할까?

`useRef`는 **값이 변경되어도 화면을 다시 그릴 필요가 없는 경우** 사용한다.

- 렌더링 사이에 기억해야 하는 값 (이전 값, interval ID 등)
- 브라우저 API와 연동하기 위한 값 (`setInterval`, `setTimeout` 등)
- DOM 요소를 직접 참조해야 하는 경우 (`focus`, `scrollIntoView`, `select`, 애니메이션 실행 등)
- 클래스나 라이브러리 객체처럼 컴포넌트마다 하나만 생성해서 재사용해야 하는 경우 (`VideoPlayer` 등)

`useRef`에 저장된 값은 변경되어도 React가 리렌더링하지 않으므로, 화면에 표시되는 데이터가 아닌 값을 저장하는 데 적합하다.

<br />

## 4. 사용할 때 주의할 점

`useRef`는 렌더링과 무관한 값을 저장하기 위한 Hook이므로 `useState`와는 사용 목적이 다르다. 다음 사항을 주의해서 사용해야 한다.

### 1. 렌더링 중에는 `ref.current`를 읽거나 쓰지 않는다.

React는 컴포넌트가 순수 함수처럼 동작하기를 기대한다.
렌더링 중 `ref.current`를 변경하면 React가 렌더링을 자유롭게 제어하기 어려워지고, 예측하기 어려운 동작이 발생할 수 있다.

되도록 이벤트 핸들러나 Effect에서 읽고 변경하는 것이 좋다.

다만, 항상 동일한 결과를 보장하는 초기화 패턴은 예외적으로 사용할 수 있다.

```tsx
const playerRef = useRef<VideoPlayer | null>(null);

if (playerRef.current === null) {
  playerRef.current = new VideoPlayer();
}
```

### 2. 화면에 표시되는 값은 useRef가 아닌 useState를 사용한다.

useRef는 값이 변경되어도 리렌더링을 발생시키지 않는다. 따라서 화면에 표시되는 데이터나 JSX 계산에 사용되는 값은 useState로 관리해야 한다.

```tsx
// ❌ 화면은 변경되지 않는다.
const nameRef = useRef("");

function handleChange(e) {
  nameRef.current = e.target.value;
}

return <input value={nameRef.current} onChange={handleChange} />;
```

### 3. useRef는 초기화 함수를 지원하지 않는다.

initialValue는 그대로 current에 저장된다. useState처럼 함수를 전달해도 실행되지 않고 함수 자체가 저장된다.

```tsx
// ❌ 함수가 실행되지 않는다.
const playerRef = useRef(() => new VideoPlayer());
```

생성 비용이 큰 객체는 앞에서 살펴본 것처럼 ref.current === null 패턴을 사용하여 한 번만 생성하는 것이 좋다.

<br />

## 한 줄 요약

> **useRef는 렌더링과 무관한 값을 컴포넌트 생명주기 동안 유지하고 참조하기 위한 Hook이다.**
