# useOptimistic

<br />

## 1. 왜 등장했을까?

네트워크 요청이 완료될 때까지 기다리면 사용자 입장에서는 UI가 느리게 느껴질 수 있다.

기존에는 `useState`를 이용해 낙관적 업데이트와 실패 시 롤백을 직접 구현해야 했다.

`useOptimistic`은 이러한 패턴을 단순하게 만들기 위해 등장했다.

서버 요청이 성공할 것이라고 가정하고 UI를 먼저 업데이트한 뒤, 실제 결과에 따라 자동으로 원래 상태와 동기화한다.

<br />

## 2. 어떤 훅일까?

```tsx
const [optimisticState, addOptimistic] = useOptimistic(state, updateFn);
```

`useOptimistic`은 실제 state와 별도로 낙관적인 UI 상태를 관리하는 Hook이다.

`addOptimistic`을 호출하면 실제 state를 변경하지 않고, 화면에만 임시 상태를 반영한다.

### 매개변수

- **state** : 실제 상태
- **updateFn** : 낙관적 상태를 계산하는 순수 함수

### 반환값

- **optimisticState** : 화면에 표시할 낙관적 상태
- **addOptimistic** : 낙관적 업데이트를 추가하는 함수

<br />

## 3. 어떤 상황에서 사용할까?

`useOptimistic`은 **서버 요청이 성공할 가능성이 높고, 사용자에게 즉각적인 피드백을 제공하고 싶은 경우** 사용한다.

- 댓글 작성
- 좋아요
- 장바구니 추가/삭제
- 체크박스 토글

```tsx
addOptimistic(newMessage);

await sendMessage();
```

사용자는 서버 응답을 기다리지 않고 즉시 변경된 UI를 확인할 수 있다.

<br />

## 4. 사용할 때 주의할 점

- `updateFn`은 순수 함수여야 한다.
- `addOptimistic`은 `startTransition` 안에서 호출하여 React가 Action의 생명주기를 추적할 수 있도록 한다.

```tsx
startTransition(async () => {
  addOptimistic(newMessage);

  await sendMessage();

  setMessages(...);
});
```

<br />

## 한 줄 요약

> **useOptimistic은 서버 요청이 성공할 것이라고 가정하고, 실제 state와 별도로 낙관적인 UI를 먼저 보여주기 위한 Hook이다.**
