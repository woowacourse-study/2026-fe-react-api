## useActionState

공식 문서의 표현

> `useActionState`는 Action을 사용해 사이드 이펙트를 동반한 상태 업데이트를 할 수 있게 해주는 React Hook입니다.

### 개념

```ts
function reducerAction(previousState, actionPayload) {
  // ...
}

const [state, dispatchAction, isPending]
	= useActionState(reducerAction, initialState, permalink?);
```

#### 매개변수

1. `reducerAction`: Action이 트리거될 때 호출될 함수

2. `initialState`: 상태의 초깃값 (`dispatchAction`이 처음 호출된 이후에는 이 인수를 무시)

#### 반환값

1. `state`: 현재 상태

2. `dispatchAction`: Action 내부에서 호출하는 dispatch 함수

3. `isPending`: 이 Hook에 대해 디스패치된 Action이 대기 중(pending)인지 알려주는 플래그

4. `useReducer`와 비슷한 동작을 수행하지만 약간 다르다.

```
useReducer:
- UI 상태를 관리
- reducer는 순수 함수여야 함
- reducer 안에서 side effect 불가능

useActionState:
- action의 결과 상태를 관리
- action 함수 비동기 가능
- action 안에서 side effect 가능
```

form 제출을 위한 상태 관리라고 볼 수 있을 것 같다. form을 위한 상태가 많아질수록 용이한 훅인 것 같다.

그리고 `useActionState`를 사용하면 action이 큐잉된다. 예를 들어 티켓 구매하기 버튼을 눌러 가격을 보여준다고 했을 때, 구매하기 버튼을 5번 누르면 5번 분량의 지연만큼 시간이 지난 후에 가격이 렌더링된다.

공식 문서에 따르면 이것은 의도된 동작이다. `useOptimistic`을 사용하거나, 중간에 action을 취소하는 방법을 사용해서 이 동작을 일으키지 않을 수 있다.

### 코드 예시

```ts
import { useActionState } from 'react';

async function increment(previousCount: number) {
  await new Promise((resolve) => setTimeout(resolve, 1000));

  return previousCount + 1;
}

export default function Counter() {
  const [count, incrementAction, isPending] = useActionState(increment, 0);

  return (
    <form action={incrementAction}>
      <p>Count: {count}</p>

      <button disabled={isPending}>{isPending ? '증가 중...' : '+1'}</button>
    </form>
  );
}
```

`increment` 메서드는 1초 이후에 숫자를 1 증가시킨다.

useActionState를 사용하면 요청 중일 때를 `isPending` 플래그로 확인할 수 있고, 여러 번 숫자를 증가시키면 큐잉되어 요청이 모두 완료 된 후에 증가된 수량이 렌더링된다.

위처럼 단순한 버튼 예시 뿐만 아니라 form action에도 사용할 수 있다.

```ts
import { useActionState } from 'react';

async function loginAction(
  previousState: LoginState,
  formData: FormData,
): Promise<LoginState> {
  const email = formData.get('email');
  const password = formData.get('password');

  if (!email || !password) {
    return {
      message: '이메일과 비밀번호를 모두 입력해주세요.',
    };
  }

  await new Promise((resolve) => setTimeout(resolve, 1000));

  if (email !== 'test@example.com' || password !== '1234') {
    return {
      message: '이메일 또는 비밀번호가 올바르지 않습니다.',
    };
  }

  return {
    message: '로그인 성공!',
  };
}

export default function LoginForm() {
  const [state, formAction, isPending] = useActionState(loginAction, {
    message: '',
  });

  return (
    <form action={formAction}>
      <input name="email" type="email" placeholder="이메일" />

      <input name="password" type="password" placeholder="비밀번호" />

      <button disabled={isPending}>
        {isPending ? '로그인 중...' : '로그인'}
      </button>

      {state.message && <p>{state.message}</p>}
    </form>
  );
}
```

이메일과 비밀번호를 입력하는 form에 대한 메세지를 useActionState로 관리한다.

`useReducer` 훅과 마찬가지로 상태를 업데이트하는 로직이 컴포넌트 외부에 존재할 수 있다. 폼 검증, 요청, 성공/실패 처리가 `loginAction` 안에 모여서 컴포넌트가 덜 복잡해지는 장점이 있다.

위와 같은 맥락으로 isPending 플래그를 통해 로딩 상태임을 나타낼 수 있다.

### 결론

폼 제출처럼 비동기 작업의 결과를 state로 관리할 때 유용한 훅이다.

상태 업데이트 로직의 위치가 자유롭고, 여러 상태로 폼을 관리하지 않아도 된다.
