## useActionState

### 나온 배경

비동기 작업 하나 돌리고 결과를 화면에 보여주는 흔한 일에 상태가 3개씩 필요했다.

```jsx
const [result, setResult] = useState(null);
const [isPending, setIsPending] = useState(false);

async function handleSubmit(e) {
  e.preventDefault();
  setIsPending(true);
  try {
    setResult(await submitOrder(new FormData(e.target)));
  } catch (err) {
    setResult({ error: err.message });
  } finally {
    setIsPending(false);
  }
}
```

불편함이 겹겹이다. pending 토글과 try/catch/finally를 폼마다 반복하고(finally 하나 빼먹으면 pending이 영원히 true), 버튼을 연타하면 두 요청이 경쟁해서 늦게 끝난 쪽이 상태를 덮어쓴다. 결정적으로 이 폼은 onSubmit이 전부라 JS 번들이 로드되기 전엔 죽어있다. 이 셋을 한 번에 풀려고 나온 게 useActionState다.

### 요약

```jsx
const [state, dispatchAction, isPending] = useActionState(actionFn, initialState, permalink?)
```

- `actionFn(prevState, payload)`: 이전 상태와 dispatch로 넘긴 값을 받아 새 상태를 반환한다. useReducer의 리듀서와 같은 모양인데, 비동기 + 사이드 이펙트가 허용된다는 게 결정적 차이다. 문서의 공식 프레이밍도 "사이드 이펙트를 수행할 수 있는 리듀서"다
- `state`: actionFn이 마지막으로 반환한 값
- `dispatchAction`: 액션 실행 함수. `<form action={...}>`에 꽂거나 startTransition 안에서 호출한다
- `isPending`: 실행 중 여부. 직접 토글하지 않아도 나온다
- dispatch를 연달아 호출하면 React가 큐에 넣고 순서대로 실행하고, 이전 액션의 반환값이 다음 액션의 prevState로 들어온다. 연타 경쟁 상태가 구조적으로 안 생긴다

React 18 카나리의 useFormState였다가, 폼 전용이 아니게 일반화되면서 React 19에서 지금 이름으로 정식화됐다.

### 예시

```jsx
function OrderForm() {
  const [state, formAction, isPending] = useActionState(
    async (prevState, formData) => {
      const result = await submitOrder(formData);
      if (result.error) return { error: result.error }; // 던지지 않고 반환
      return { success: true };
    },
    null,
  );

  return (
    <form action={formAction}>
      <input name="quantity" />
      <button disabled={isPending}>주문</button>
      {state?.error && <p>{state.error}</p>}
    </form>
  );
}
```

`e.preventDefault()`도 `new FormData(e.target)`도 없다. form의 action에 꽂으면 React가 FormData를 만들어 payload로 넘겨준다. 액션 함수가 Server Function이면 JS 로드 전에도 폼이 동작하고, 세 번째 인자 permalink는 그 시나리오에서 JS 로드 전 제출 시 이동할 URL이다.

### 주의사항

- 예상 가능한 실패(검증 에러 등)는 던지지 말고 상태로 반환한다. 던지면 Error Boundary로 가고 대기 중인 액션 큐가 전부 취소된다.
- dispatchAction을 이벤트 핸들러에서 직접 부르면 에러다. form action이나 startTransition 안에서만 호출할 수 있다. day4의 useTransition이 여기서 다시 나온다 — dispatch가 내부적으로 transition으로 처리되고, isPending이 공짜인 이유다.
- 폼에서 쓸 때 헷갈리는 포인트: FormData는 두 번째 인자다. 첫 번째는 항상 prevState.
- Server Function과 쓰면 initialState와 payload가 직렬화 가능해야 한다.
- 순수 상태 전이면 useReducer, 네트워크가 끼면 useActionState로 가르면 된다.
