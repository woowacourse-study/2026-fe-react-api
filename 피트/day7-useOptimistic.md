## useOptimistic

공식 문서의 표현

> `useOptimistic`은 UI를 낙관적으로 업데이트할 수 있게 해주는 React Hook입니다.

### 코드

```ts
const [optimisticState, setOptimistic] = useOptimistic(value, reducer?);
```

`useReducer` 훅과 유사하게 초기값과 `reducer` 함수를 전달해서, `state`와 `dispatch` 함수를 반환한다.

### 예시

```ts
async function saveLike(nextLiked: boolean) {
  await new Promise((resolve) => setTimeout(resolve, 1000));

  return nextLiked;
}

export default function LikeButton() {
  const [isLiked, setIsLiked] = useState(false);

  const [optimisticLiked, setOptimisticLiked] = useOptimistic(isLiked);

  function handleClick() {
    const nextLiked = !optimisticLiked;

    startTransition(async () => {
      setOptimisticLiked(nextLiked);

      const savedLiked = await saveLike(nextLiked);

      setIsLiked(savedLiked);
    });
  }

  return (
    <button onClick={handleClick}>
      {optimisticLiked ? '좋아요 취소' : '좋아요'}
    </button>
  );
}
```

좋아요 버튼을 예시로 보자. 현재 좋아요 상태를 매개변수로 전달한 `useOptimistic` 훅의 반환값들을 action에 사용한다.

먼저 `setOptimisticLiked` 메서드를 통해 낙관적으로 업데이트하고, 이후 요청이 완료되면 실제 상태에 업데이트한다.

### 결론

낙관적으로 업데이트해야 할 때 유용한 훅이다.

장바구니 상품의 수량 변경, 배송 조건 변경 버튼, 좋아요 버튼 등 UX 측면에서 사용자에게 답답함을 줄여줄 수 있다.
