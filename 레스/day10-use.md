## use

### 나온 배경

두 갈래의 불편함을 하나의 API로 푼다.

첫째, 렌더링 중에 Promise를 읽을 방법이 없었다. 클라이언트에서 데이터를 가져오는 코드는 이 패턴이 강제였다.

```jsx
const [message, setMessage] = useState(null);
useEffect(() => {
  fetchMessage().then(setMessage);
}, []);
if (message === null) return <Spinner />;
```

렌더링 한 번 → 이펙트 실행 → 상태 변경 → 다시 렌더링을 돌아야 데이터가 화면에 도착하고, 로딩·에러 분기는 컴포넌트마다 수동이다. Suspense라는 선언적 로딩 장치는 React 18부터 있었지만, "이 Promise가 끝날 때까지 나를 매달아줘(suspend)"라고 말할 공식 수단이 애플리케이션 코드엔 없었다. react-query 같은 라이브러리만 내부 트릭으로 하던 걸 공식화한 게 use다.

둘째, useContext는 훅 규칙 때문에 조건문 안에서 호출할 수 없었다. early return 뒤에서만 필요해도 무조건 함수 맨 위로 끌어올려야 했다.

### 요약

```jsx
const value = use(resource); // resource: Promise 또는 Context
```

- 문서가 Hook이 아니라 API라고 부른다. 훅 규칙을 일부러 깬다 — 조건문, 반복문 안에서 호출 가능
- `use(Context)`: useContext와 동일하되 조건부 호출이 되는 버전
- `use(Promise)`: pending이면 컴포넌트가 suspend되어 가장 가까운 `<Suspense>` fallback이 뜨고, resolve되면 그 값으로 재개, reject되면 가장 가까운 Error Boundary로 간다
- 로딩·에러 분기가 컴포넌트 코드에서 사라지고 트리 구조(Suspense/Error Boundary 배치)로 옮겨간다

### 예시 — 서버에서 클라이언트로 Promise 넘기기

문서가 미는 대표 패턴. 서버 컴포넌트에서 await하면 데이터가 준비될 때까지 서버 렌더링이 통째로 막힌다. Promise를 기다리지 않고 prop으로 넘기면 서버는 나머지 HTML을 먼저 스트리밍하고, 클라이언트 컴포넌트가 use로 받아 자기만 suspend한다.

```jsx
// 서버 컴포넌트 — await 안 함
const messagePromise = fetchMessage();
return (
  <Suspense fallback={<p>불러오는 중...</p>}>
    <Message messagePromise={messagePromise} />
  </Suspense>
);
```

```jsx
'use client';
export function Message({ messagePromise }) {
  const message = use(messagePromise);
  return <p>{message}</p>;
}
```

조건부 context 읽기는 이렇게 된다.

```jsx
function HorizontalRule({ show }) {
  if (show) {
    const theme = use(ThemeContext);
    return <hr className={theme} />;
  }
  return false;
}
```

### 렌더링 중에 Promise를 만들면 안 된다

```jsx
// 무한 suspend
function Message() {
  const message = use(fetchMessage()); // 렌더링마다 새 Promise
  ...
}
```

suspend됐다가 재개되면 다시 렌더링되는데, 그때 또 새 Promise가 만들어지니 영원히 resolve된 값을 못 본다. use에 넘기는 Promise는 안정된 것이어야 한다 — 서버에서 내려온 것이거나, 프레임워크·라이브러리 캐시에서 나온 것. day8에서 getSnapshot이 매번 새 객체를 반환하면 무한 루프였던 것과 같은 계열의 규칙이다.

### 주의사항

- try/catch 안에서 호출할 수 없다. suspend가 내부적으로 예외 던지기로 구현돼 있어서다("Suspense Exception: This is not a real error!"가 그 흔적). 거부 처리는 Error Boundary로 받거나 Promise 쪽에 미리 `.catch()`를 붙인다.
- 서버 컴포넌트 안에서는 그냥 async/await가 권장이다. use는 클라이언트 쪽 도구고, 서버에서 넘기는 Promise의 resolve 값은 직렬화 가능해야 한다.
- `use(context)`는 호출한 컴포넌트 위쪽의 Provider만 찾는다. 같은 컴포넌트 안에서 렌더링한 Provider는 고려하지 않는다.
- day4~5가 업데이트의 긴급도를 React에 넘기는 훅이었다면, use는 비동기 데이터의 대기를 React에 넘기는 API다. 로딩 상태를 내가 관리하는 게 아니라 React 트리가 관리하게 된다.
