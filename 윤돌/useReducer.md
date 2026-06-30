# useReducer

<br />

## 1. 왜 등장했을까?

useState는 상태와 상태를 변경하는 함수를 제공하는 단순한 Hook이다.
따라서 상태 변경 로직이 복잡해질수록 이벤트 핸들러마다 `set` 함수가 늘어나고,
상태 변경 규칙이 여러 곳에 흩어질 수 있다.

useReducer는 이러한 문제를 해결하기 위해 등장했다.
**상태를 어떻게 변경할지에 대한 규칙을 reducer에 모아두고**,  
**이벤트 핸들러에서는 무엇을 하고 싶은지만 dispatch로 요청하도록 역할을 분리한다.**

이를 통해 **상태 변경 로직을 한곳에서 관리**할 수 있고,
이벤트 핸들러는 **상태 변경의 세부 구현을 몰라도 되므로** 코드의 응집도를 높이고 변경에 유연하게 대응할 수 있다.

<br />

## 2. 어떤 훅일까?

```tsx
const [state, dispatch] = useReducer(reducer, initialState);
```

`useReducer`는 상태 변경 규칙을 reducer 함수에 모아 관리하는 Hook이다. 이벤트 핸들러에서는 `dispatch`를 통해 수행할 작업만 전달하고, 실제 상태 변경은 reducer가 담당한다.

### 매개변수

- **reducer** : 현재 state와 action을 받아 다음 state를 반환하는 순수 함수
- **initialState** : 상태의 초기값
- **init?** : `initialState`를 받아 초기 state를 생성하는 초기화 함수

### 반환값

- **현재 상태(state)**
- **dispatch 함수** : action을 전달하여 상태 변경을 요청한다.

<br />

## 3. 어떤 상황에서 사용할까?

`useReducer`는 상태 변경 로직이 단순한 값 변경을 넘어, 여러 규칙을 가지기 시작할 때 사용한다.

- 하나의 이벤트에서 여러 state가 함께 변경될 때
- 다음 state가 이전 state와 action에 따라 결정될 때
- 상태 변경 규칙이 여러 이벤트 핸들러에 흩어질 때
- 이벤트 핸들러가 `set` 함수 호출로 길어질 때
- 상태 변경 과정을 `추가`, `삭제`, `수정`, `초기화` 같은 action 단위로 표현하고 싶을 때

`useState`는 단순한 상태를 직접 변경하기에 적합하고, `useReducer`는 상태 변경 규칙을 reducer에 모아두고 이벤트 핸들러에서는 **수행할 작업(action)만 dispatch로 전달**한다.

```tsx
function reducer(state, action) {
  switch (action.type) {
    case "incremented_age": {
      return {
        name: state.name,
        age: state.age + 1,
      };
    }
    case "changed_name": {
      return {
        name: action.nextName,
        age: state.age,
      };
    }
  }
  throw Error("Unknown action: " + action.type);
}
```

이처럼 보통 reducer에서는 `switch` 문을 사용하여 `action.type`에 따라 상태 변경 규칙을 분기한다.

이렇게 하면 이벤트 핸들러는 상태 변경의 세부 구현을 몰라도 되고, reducer가 상태를 어떻게 변경할지 결정한다.

```tsx
function handleButtonClick() {
  dispatch({ type: "incremented_age" });
}
```

<br />

## 4. 사용할 때 주의할 점

`useReducer`는 `useState`와 동일한 state 업데이트 규칙을 따른다.

- `reducer`는 순수 함수여야 한다.
- `dispatch`는 현재 렌더링의 state를 즉시 변경하지 않고, 다음 렌더링에서 반영된다.
- 여러 `dispatch` 호출은 React의 batching 대상이 된다.
- `reducer`에서는 state를 직접 변경하지 말고 새로운 state를 반환해야 한다.
- `useState`와 마찬가지로 React는 `Object.is`를 사용하여 state를 비교한다.

<br />

## 한 줄 요약

> **useReducer는 상태 변경 규칙을 reducer에 모아 관리하고, 이벤트 핸들러에서는 수행할 작업만 요청할 수 있도록 하는 Hook이다.**
