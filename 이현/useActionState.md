## useActionState

Action을 사용해 사이드 이펙트를 동반한 상태 업데이트를 해주는 훅

### 키워드

### 요약

useReducer와 굉장히 유사하다. 차이점이라면 useActionState는 api 요청같은 사이드 이펙트를 동반할 수 있다.
따라서 isPending같은 로딩상태도 사용할 수 있다.
action을 위해서 나온 것이기 때문에 dispatchAction은 action 내에서만 사용할 수 있다.

react에서 state를 사용하는 방법 중 하나이다.

### 주요 용도

주로 form을 다룰 때 사용한다.
여러 훅 선언 없이 이 훅 하나로 사용자의 상호작용으로 변경되는 상태, 로딩 상태 등을 관리할 수 있다.

### 예시

```tsx
const [count, dispatchAction, isPending] = useActionState(updateCartAction, 0);

function handleAdd() {
  startTransition(() => {
    dispatchAction({ type: "ADD" });
  });
}

function handleRemove() {
  startTransition(() => {
    dispatchAction({ type: "REMOVE" });
  });
}

...

async function updateCartAction(prevCount, actionPayload) {
  switch (actionPayload.type) {
    case "ADD": {
      return await addToCart(prevCount);
    }
    case "REMOVE": {
      return await removeFromCart(prevCount);
    }
  }
  return prevCount;
}
```
