## useEffectEvent

### 나온 배경

React 19.2부터 정식 API이다. 

Effect 안에서 어떤 값(props나 state)을 읽을 때,

1. 의존성 배열에 넣으면 → 값이 바뀔 때마다 Effect가 재실행됨 (원치 않음)
2. 넣지 않으면 → stale한 값을 읽고 린터가 경고함

### 요약

Effect의 비반응형 로직을 떼어내 Effect 이벤트로 만드는 Hook.

- 반응형 로직: 값이 바뀌면 재실행돼야 함 (roomId 바뀌면 재연결) → Effect에
- 비반응형 로직: 최신 값만 읽으면 됨 (연결 시 최신 theme로 알림) → Effect Event로

즉 "최신 값 읽기"와 "값 변화 시 재실행"을 떼어놓는다.

### 실행 타이밍

`useEffect`, `useLayoutEffect`, `useInsertionEffect` 내부에서만 호출한다.
이벤트 핸들러처럼 반응해서 호출되며, 렌더링 중 직접 호출은 금지(Error).

### 예시

```tsx
function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification("연결됨!", theme); // 항상 최신 theme를 읽음
  });

  useEffect(() => {
    const connection = createConnection(roomId);
    connection.on("connected", () => onConnected());
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // theme는 의존성에 없어도 됨
}
```

theme가 바뀌어도 재연결은 안 일어나고, 알림은 항상 최신 테마로 뜬다.

### 주의사항

- Effect 내부에서만 호출. 다른 컴포넌트나 Hook으로 전달 금지.
- 의존성 배열에 넣지 않는다 (린터도 제외함).
