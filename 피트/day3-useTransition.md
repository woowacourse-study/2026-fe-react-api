# useTransition

공식 문서의 표현

> UI의 일부를 백그라운드에서 렌더링 할 수 있도록 해주는 React Hook입니다.

### 코드

```ts
const [isPending, startTransition] = useTransition();
```

매개변수는 없고, 두 값을 반환한다.

1. `isPending`: 대기 중인 transition이 있는지 여부

2. `startTransition`: 업데이트를 Transition으로 표시할 수 있게 해주는 함수

`startTransition` 메서드의 콜백함수 안에서 상태를 업데이트하는 로직을 넣을 수 있다.

### useTransition 왜 쓸까?

모든 상태에 대해서 렌더링이 동기적으로 일어나면 좋지만, 현실적으로 불가능하므로 상태가 늦게 반응하는 경우가 생긴다. 상태 업데이트가 느리면 그만큼 렌더 트리가 블락되므로 ux에 좋지 않은 영향을 준다.

startTransition 메서드를 통해 우선순위가 낮은 상태 업데이트를 transition이라고 표시해서 React가 UI 렌더링 시 우선순위에 따라서 업데이트할 수 있도록 도와준다.

### 코드 예시

우리 장바구니 미션에서, 상품의 수량을 변경하는 로직에 대해 생각해보자. 아이템의 수량을 변경하는 요청이 완료되기까지 걸리는 시간이 크다면, 여러 번 업데이트되는 문제가 발생한다.

위 문제의 일반적인 해결책은 요청중의 수량 변경 자체를 막는 것이다. 근데 이 해결책은 사용자 입장에서 앱이 느리게 느껴질 수 있고, UX 측면에서 좋지 않다고 생각한다.

그래서 `useTransition` 훅은 이러한 경우를 간단하게 처리할 수 있도록 내장 API를 제공한다!

```ts
function App({}) {
  const [quantity, setQuantity] = useState(1);
  const [isPending, startTransition] = useTransition();

  const updateQuantityAction = async (newQuantity) => {
    // transition의 보류 중인 상태에 액세스하려면,
    // startTransition을 다시 호출하세요.
    startTransition(async () => {
      const savedQuantity = await updateQuantity(newQuantity);
      startTransition(() => {
        setQuantity(savedQuantity);
      });
    });
  };

  return (
    <div>
      <h1>Checkout</h1>
      <Item action={updateQuantityAction} />
      <hr />
      <Total quantity={quantity} isPending={isPending} />
    </div>
  );
}
```

`useTransition` 훅을 적용한 코드이다. 위 코드에서 수량 업데이트를 빠르게 수행하면, 요청이 진행 중에는 `Total` 상태가 대기 중으로 표시된다.

이후 최종 요청이 완료된 후에야 `Total`이 업데이트된다. 업데이트가 Action 내에서 발생하기 때문에, 요청이 진행 중인 동안에도 수량을 계속 업데이트할 수 있다.

### 결론

기본적으로 UX를 위한 훅이다. 사용자의 클릭이나 선택, 변경 등에 적용할 수 있고, 그 작업들 중 무거운지 아닌지 여부에 따라서 우선순위를 매겨서 사용자가 느낄 수 있는 답답한 상황을 해소할 수 있다.

`useTransition`은 UX를 개선하기 위한 훅이다. 사용자의 클릭, 입력, 선택처럼 즉시 반응해야 하는 업데이트와 무거운 렌더링을 유발하는 업데이트를 구분할 수 있게 해준다.

급한 업데이트는 바로 처리하고, 덜 급한 업데이트는 transition으로 표시해서 React가 낮은 우선순위로 처리하게 만들 수 있다.

따라서 검색 결과 렌더링, 무거운 탭 전환, 큰 리스트 업데이트, 계산 비용이 큰 UI 갱신처럼 사용자가 기다림을 느낄 수 있는 상황에서 유용하다.
