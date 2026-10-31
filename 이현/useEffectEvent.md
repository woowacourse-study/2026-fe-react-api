## useEffectEvent

### 키워드

### 요약

Effect 내부의 비반응형 로직을 Effect Event라는 함수로 분리할 수 있게 해주는 React Hook이다.

Effect 내부에선 2가지 로직이 존재할 수 있다.

1. 값이 변경되면 Effect도 다시 실행되어야 하는 반응형 로직 (deps에 있는거)
2. 사용하는 값이 변경되어도 effect는 다시 실행되지 않지만, 외부로부터 항상 최신 상태의 값을 받아와 사용하는 경우 (deps에는 넣으면 안됨)

여기서 2번째 로직을 분리하기 위해 사용하는 것이 이 훅의 용도이다.

### 주요 용도

브라우저같은 외부 세계의 이벤트 리스너 등록을 첫 마운트 때만 해야할 때

조금 더 범용적으로는 말하면 effect외부에 정의된 값을 쓰는 로직이지만, deps에 넣을 필요가 없을 때

### 예시

```tsx
const onTick = useEffectEvent(() => {
  setCount(count + increment);
});

useEffect(() => {
  const id = setInterval(() => {
    onTick();
  }, 1000);
  return () => {
    clearInterval(id);
  };
}, []);
```

count 또는 increment가 바뀔 때마다 interval을 등록할 필요는 없다.
첫 마운트 될 때만 interval을 등록하면 되므로 useEffectEvent 훅을 사용한다.

```tsx
useEffect(() => {
  const id = setInterval(() => {
    setCount(count + increment);
  }, 1000);
  return () => {
    clearInterval(id);
  };
}, [count, increment]);
```

만약 위처럼 useEffectEvent를 쓰지 않는다면?
일단 count가 바뀔 때마다 새로운 interval이 계속 등록되는 것이 첫 번째 문제이다.
그다음이 더 심각한데, interval 주기보다 빠르게 increment를 연속으로 변경하는 경우 interval등록과 cleanup이 계속 반복되고, interval의 대기시간이 계속 초기화 돼서 increment 변경이 끝날 때까지 카운터가 작동하지 않는다.
