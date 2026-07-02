# useReducer

> `useReducer`는 컴포넌트에 reducer를 추가하는 React Hook입니다.

`useReducer`는 `useState`와 마찬가지로 상태 관리를 다루는 훅 중 하나이다.  
`useState`와 비슷한 역할을 수행하지만, 조금 더 구조화된 방식으로 상태를 관리할 수 있도록 도와준다.

```ts
const [state, dispatch] = useReducer(reducer, initialArg, init?);
```

`state`는 현재 상태이고, `dispatch`는 상태 업데이트에 필요한 action을 전달하는 함수이다.

`reducer`는 현재 state와 action을 받아 다음 state를 반환하는 순수 함수이다.

## 1. useReducer

## 1-1. 매개변수

**reducer**: state 업데이트 방식을 지정하는 순수 함수

**initialArg**: state 초기 값에 사용할 값

**(optional) init**: 초기 state를 계산하는 초기화 함수

`init`을 전달하면 초기 state는 `initialArg`가 아니라 `init(initialArg)`의 반환값이 된다.

## 1-2. 반환값

**state**: 현재 state 값

**dispatch**: action을 전달해서 state 업데이트와 리렌더링을 요청하는 함수

## 2. reducer

## 2-1. 매개변수

**state**: 현재 state

**action**: `dispatch` 함수로부터 전달받은 값

## 2-2. 반환값

다음 state

reducer는 반드시 순수 함수여야 한다.

즉, 같은 `state`와 `action`이 들어오면 항상 같은 결과를 반환해야 하고, 기존 state를 직접 수정하면 안 된다.

## 3. dispatch

## 3-1. 매개변수

**action**: reducer에 전달할 값

action은 어떤 형태든 가능하지만, 보통 `type` 프로퍼티를 포함한 객체 형태를 사용한다.

```ts
dispatch({ type: 'incremented_age' });
```

action은 state 전체를 담는 객체가 아니다.  
다음 state를 계산하는 데 필요한 최소한의 정보만 담으면 된다.

예를 들어 `name`만 변경해야 한다면 action에는 `name` 변경에 필요한 값만 담으면 된다.

```ts
dispatch({
  type: 'changed_name',
  nextName: 'Taylor',
});
```

## 기본적인 흐름

1. `initialArg`로 전달한 값이 초기 state가 된다.
2. `reducer`를 정의해서 `useReducer`의 첫 번째 인자로 넘긴다.
3. 이벤트 핸들러에서 `dispatch(action)`을 호출한다.
4. React가 현재 state와 action을 reducer에 전달한다.
5. reducer가 다음 state를 반환한다.
6. React가 다음 렌더링에서 새로운 state를 반영한다.

## 코드 예시

```tsx
import { useReducer } from 'react';

type State = {
  name: string;
  age: number;
};

type Action =
  | { type: 'incremented_age' }
  | { type: 'changed_name'; nextName: string };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case 'incremented_age': {
      return {
        ...state,
        age: state.age + 1,
      };
    }

    case 'changed_name': {
      return {
        ...state,
        name: action.nextName,
      };
    }

    default: {
      throw new Error('Unknown action');
    }
  }
}

const initialState: State = {
  name: 'Taylor',
  age: 42,
};

export default function Form() {
  const [state, dispatch] = useReducer(reducer, initialState);

  function handleButtonClick() {
    dispatch({ type: 'incremented_age' });
  }

  function handleInputChange(e: React.ChangeEvent<HTMLInputElement>) {
    dispatch({
      type: 'changed_name',
      nextName: e.target.value,
    });
  }

  return (
    <>
      <input value={state.name} onChange={handleInputChange} />
      <button onClick={handleButtonClick}>Increment age</button>
      <p>
        Hello, {state.name}. You are {state.age}.
      </p>
    </>
  );
}
```

reducer를 정의하고, 반환된 `dispatch` 함수의 인자로 `type` 프로퍼티를 포함한 action 객체를 넘겨서 해당 동작을 수행한다.

`useState`와의 큰 차이점은 상태 업데이트 로직을 컴포넌트 외부의 reducer로 분리할 수 있다는 점이다.

또한 여러 상태가 하나의 동작에 의해 함께 바뀌는 경우, action 단위로 상태 변화를 표현할 수 있어 흐름을 파악하기 좋다.

## action

`dispatch`의 인자로 넘겨주는 action은 다양한 형태가 될 수 있다.

그러나 실무에서는 보통 `type` 프로퍼티를 포함한 객체 형태를 사용한다.

```ts
dispatch({
  type: 'submitted_answer',
  questionId: 3,
  answer: 'A',
});
```

action은 "다음 state 그 자체"라기보다 "무슨 일이 일어났는지"를 표현하는 값에 가깝다.

따라서 action의 프로퍼티가 반드시 state의 프로퍼티와 1:1로 대응할 필요는 없다.

예를 들어 state에 `questionId`나 `answer`라는 프로퍼티가 그대로 없더라도, reducer가 이 정보를 이용해 다음 state를 만들 수 있다면 괜찮다.

## 주의사항

`useState`와 마찬가지로, `useReducer`의 state도 스냅샷처럼 동작한다.

따라서 `dispatch`를 호출한 직후 같은 함수 안에서 state를 출력해도 업데이트된 값이 아니라 현재 렌더링 시점의 이전 값이 출력된다.

```ts
function handleClick() {
  dispatch({ type: 'incremented_age' });
  console.log(state.age); // 아직 이전 렌더링의 state
}
```

또한 reducer 안에서는 기존 state를 직접 수정하면 안 된다.

```ts
// 좋지 않은 예
state.age += 1;
return state;
```

기존 state를 복사해서 새로운 state를 반환해야 한다.

```ts
// 좋은 예
return {
  ...state,
  age: state.age + 1,
};
```

## useReducer의 장점

복잡한 구조의 state를 다루는 경우 `useReducer`가 가독성이 더 좋을 수 있다.

특히 어떤 동작을 수행하는지, 어떤 이벤트가 발생했는지를 action 이름으로 명확하게 표현할 수 있다.

```ts
dispatch({ type: 'opened_modal' });
dispatch({ type: 'submitted_form' });
dispatch({ type: 'reset_form' });
```

또한 상태 변경 로직이 reducer에 모이기 때문에, 컴포넌트 내부의 이벤트 핸들러가 상대적으로 단순해진다.

디버깅할 때도 reducer 내부에서 action과 state 변화를 확인하기 쉽다.

```ts
function reducer(state: State, action: Action): State {
  console.log('action:', action);
  console.log('prev state:', state);

  // ...
}
```

## 내 생각

useReducer에 대해 공부하고도 필요성을 크게 느끼지 못했다.

대신 상태가 복잡할 때를 가정해보자.

```ts
const personState = {
  name: 'Ahn',
  age: 24,
  address: {
    country: 'Korea',
    city: 'Seoul',
  },
  whatHave: {
    home: {
      price: 1_000_000,
      address: {
        city: 'Seoul',
        detail: 'Gangnam 23',
      },
    },
    money: 1_000_000,
  },
};
```

버튼을 누를 때마다 집값이 2배가 된다고 해보자.  
그럼 `state.whatHave.home.price`를 변경해야 한다.

`useState`로 작성하면 다음과 같은 코드가 필요하다.

```ts
setState({
  ...state,
  whatHave: {
    ...state.whatHave,
    home: {
      ...state.whatHave.home,
      price: state.whatHave.home.price * 2,
    },
  },
});
```

이러한 로직들이 여러 개라면 읽기도 어렵고 파악도 어려울 수 있다.

이걸 `useReducer`로 관리하게 된다면 코드는 이렇게 바뀐다.

```ts
type Action = {
  type: 'increased_home_price_double';
};

function reducer(state, action: Action) {
  switch (action.type) {
    case 'increased_home_price_double': {
      return {
        ...state,
        whatHave: {
          ...state.whatHave,
          home: {
            ...state.whatHave.home,
            price: state.whatHave.home.price * 2,
          },
        },
      };
    }

    default: {
      throw new Error('[ERROR] unknown action type');
    }
  }
}

// 사용하는 부분
dispatch({ type: 'increased_home_price_double' });
```

`useReducer`는 중첩 객체 업데이트를 자동으로 쉽게 만들어주는 도구는 아니다.  
대신 복잡한 상태 변경 로직을 reducer 안으로 모으고, 컴포넌트에서는 action만 dispatch하도록 만들어준다.

그래서 사용하는 쪽에서는 다음처럼 의도가 명확해진다.

```ts
dispatch({ type: 'increased_home_price_double' });
```

하지만 상태 구조가 지나치게 깊다면 `useReducer`만으로 해결하려고 하기보다 state 구조를 더 평평하게 만들거나, 필요한 경우 Immer 같은 도구를 함께 고려할 수 있다. (실제로 공식문서에서도 Immer를 언급한다)

또한 행동을 미리 정의해야 하므로, 상황에 따라 매번 다른 방식으로 단순한 값 하나만 바꾸는 경우에는 `useReducer`가 오히려 비용이 더 클 수 있다.

## 정리

`useReducer`는 state를 업데이트하는 방식 자체를 action과 reducer로 분리하는 훅이다.

다음과 같은 상황에서 사용하면 좋다.

- 여러 state가 하나의 이벤트로 함께 바뀔 때
- 상태 변경 로직이 길거나 복잡할 때
- 상태 변경의 종류를 action 이름으로 명확히 표현하고 싶을 때

반대로 단순한 input 값, toggle 값, count 값처럼 상태 변경이 간단하다면 `useState`가 더 적합하다.

한 마디로 정리하면, `useReducer`는 복잡한 상태 변경 로직을 컴포넌트 바깥으로 분리하고, action 중심으로 상태 흐름을 명확하게 만들고 싶을 때 사용하는 훅이다.
