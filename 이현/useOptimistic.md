## useOptimistic

### 요약

UI를 낙관적으로 업데이트할 수 있게 해주는 훅
비동기 작업이 진행 중일 때 다른 상태를 보여줄 수 있게 해줌.

const [optimisticState, setOptimistic] = useOptimistic(value, reducer?);

optimisticState는 사용자한테 낙관적인 값으로 사용자에게 보여줄 때 사용하면 된다.
setOptimistic은 서버 응답 전에 화면을 먼저 바꿔주는 역할을 한다.

reducer가 없을 때는 setOptimistic에 값 자체를 넣거나 함수형 업데이트를 이용해서 낙관적 값을 업데이트할 수 있다.
reducer가 있을 때는 값만 넣으면 그 값을 reducer의 로직을 사용해 낙관적 값을 업데이트 한다.

중요한 점은 setOptimistic 함수는 Action 내에서만 수행되어야 한다는 점이다.
-> action이라고 하면 startTransition내부 또는 form의 action에 들어가는 함수이다.

### 주요 용도

낙관적 업데이트가 필요할때 (서버 요청보다 빠르게 사용자 피드백을 주어야할 때)
주로 좋아요 상태, 팔로우 수 정도가 될 듯 하다.

### 예시

```tsx
const [optimisticTodos, addOptimisticTodo] = useOptimistic(
  todos,
  (currentTodos, newTodo) => [
    ...currentTodos,
    { id: newTodo.id, text: newTodo.text, pending: true },
  ],
);

function handleAddTodo(text) {
  const newTodo = { id: crypto.randomUUID(), text: text };
  startTransition(async () => {
    addOptimisticTodo(newTodo);
    await addTodoAction(newTodo);
  });
}
```

useOptimistic은 Action이 끝나면 optimisticState가 제거된다.
비동기 요청의 성공/실패 여부에 따라 롤백을 시켜주는 것이 아니라 덮어씌워져 있는 optimisticState를 지우는 것이다.
