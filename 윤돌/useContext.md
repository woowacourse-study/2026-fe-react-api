# useContext

<br />

## 1. 왜 등장했을까?

React에서는 부모 컴포넌트가 가진 값을 자식에게 전달하기 위해 Props를 사용한다.

하지만 깊은 컴포넌트에서 값이 필요한 경우, 중간 컴포넌트가 해당 값을 사용하지 않더라도 단순히 전달하기 위해 Props를 계속 넘겨야 한다.

이렇게 되면 중간 컴포넌트는 **값을 전달하는 책임**을 가지게 되고, 컴포넌트 간 결합도가 높아질 수 있다.

`useContext`는 이러한 문제를 해결하기 위해 등장했다.
중간 컴포넌트를 거치지 않고, 필요한 컴포넌트가 직접 값을 읽고 **구독**할 수 있는 통로를 제공한다.

<br />

## 2. 어떤 훅일까?

```tsx
const value = useContext(SomeContext);
```

`useContext`는 가장 가까운 Context Provider가 제공하는 값을 읽고 구독하기 위한 Hook이다.

Props를 여러 단계 전달하지 않아도 필요한 컴포넌트에서 직접 Context 값을 사용할 수 있으며, Context 값이 변경되면 이를 사용하는 컴포넌트는 함께 다시 렌더링된다.

### 매개변수

- **SomeContext** : `createContext`로 생성한 Context 객체

### 반환값

- **Context 값** : 가장 가까운 Provider가 전달한 `value`

<br />

## 3. 어떤 상황에서 사용할까?

`useContext`는 **여러 컴포넌트가 동일한 값을 공유해야 하는 경우** 사용한다.

- 테마(Theme)
- 로그인한 사용자 정보(User)
- 다국어(Locale)
- 전역 설정(Config)
- 하나의 상태를 여러 컴포넌트에서 함께 사용하는 경우

`useContext`는 값을 여러 단계의 Props로 전달하지 않고, 필요한 컴포넌트에서 직접 사용할 수 있도록 해준다.

```tsx
<ThemeContext value={theme}>
  <App />
</ThemeContext>
```

```tsx
const theme = useContext(ThemeContext);
```

추가로 `useReducer`와 함께 사용하는 경우가 많다.

`useReducer`가 상태 변경 규칙을 관리하고, `useContext`가 해당 상태와 `dispatch`를 필요한 컴포넌트에 전달한다.

이를 하나의 Provider 컴포넌트로 묶으면 **도메인 단위의 상태 관리와 값 전달을 하나의 통로로 구성**할 수 있다.

```tsx
const TodoStateContext = createContext(null);
const TodoDispatchContext = createContext(null);

function TodoProvider({ children }) {
  const [state, dispatch] = useReducer(todoReducer, initialState);

  return (
    <TodoStateContext value={state}>
      <TodoDispatchContext value={dispatch}>{children}</TodoDispatchContext>
    </TodoStateContext>
  );
}
```

이후에는 `useTodo()`와 같은 전용 Hook을 만들어 도메인의 상태를 더욱 쉽게 사용할 수 있다.

<br />

## 4. 사용할 때 주의할 점

`useContext`는 **상태를 관리하는 Hook이 아니라 값을 전달하고 구독하는 Hook**이다.

- Context 값이 변경되면 해당 Context를 사용하는 컴포넌트들이 모두 다시 렌더링된다.
- 자주 변경되는 값을 하나의 Context에 넣으면 불필요한 리렌더링이 발생할 수 있다.
- Props만으로 충분한 경우에는 Context를 사용할 필요가 없다.

<br />

## 한 줄 요약

> **useContext는 여러 단계의 Props 전달 없이 필요한 컴포넌트가 Context 값을 전달받고 구독할 수 있도록 하는 Hook이다.**
