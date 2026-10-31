## useDebugValue

### 나온 배경

React DevTools에서 커스텀 훅에 라벨을 달아주는 Hook.

커스텀 훅은 로직만 재사용하는 도구라서, 팀 공용 훅(useAuth, usePermission 등)이나 라이브러리 훅(useQuery, useSWR)이 되는 순간 쓰는 사람과 만든 사람이 분리된다. 쓰는 사람이 DevTools를 열면 보이는 건 내부 훅들의 원시값뿐이다.

```
Auth
  State: null
  State: "loading"
  Ref: {...}
```

`null`이 "아직 로딩 중이라 null"인지 "비로그인이라 null"인지 구분이 안 된다. 내부 구현을 모르면 해석할 수 없는 값들이다. 이 간극을 메우려고 나온 게 useDebugValue다.

### 요약

```jsx
useDebugValue(value, format?)
```

- 커스텀 훅 최상위에서 호출하면 DevTools의 훅 목록에 `Auth: "세션 확인 중"`처럼 라벨이 붙는다
- 반환값 없음. 앱 동작에 영향 제로. 프로덕션에서도 그냥 아무것도 안 하는 호출이다
- DevTools가 열려서 그 훅을 실제로 검사할 때만 동작하므로 평소 비용이 없다
- 컴포넌트에서는 의미 없다. 커스텀 훅 전용

### 예시

```jsx
function useAuth() {
  const [user, setUser] = useState(null);
  const [status, setStatus] = useState('loading'); // loading | authenticated | anonymous

  useDebugValue(
    status === 'loading' ? '세션 확인 중'
    : user ? `로그인: ${user.name}`
    : '비로그인'
  );

  return { user, status };
}
```

"로그인했는데 왜 로그인 페이지로 튕기지?" 하고 DevTools를 열면 `Auth: "세션 확인 중"`이 바로 보인다. 로딩 중인데 비로그인으로 판단해서 리다이렉트하는 버그라는 걸 원시값 해석 없이 알 수 있다.

내부 상태 여러 개를 사람이 읽을 한 줄로 요약하는 게 이 훅의 존재 이유 전부이다.

### 포매팅 지연하기

라벨 문자열 만드는 비용이 크면 두 번째 인자로 함수를 넘긴다.

```jsx
useDebugValue(date, date => date.toDateString());
```

첫 번째 인자에서 바로 계산하면 매 렌더링마다 비용을 내지만, format 함수로 넘기면 React는 함수만 들고 있다가 개발자가 DevTools에서 실제로 들여다보는 순간에만 실행한다. `useState(() => ...)`의 lazy initializer와 같은 발상이다.

### 주의사항

- 문서가 직접 못 박는다: **모든 커스텀 훅에 달지 마라.** 실질 기준은 "훅을 연 사람이 내부 구현을 머리에 안 들고 있는가"다. 외부 배포 라이브러리가 아니어도 팀 공용 훅이면 자격이 있다.
- 반대로 `useState` 하나 감싼 일회용 훅이면 어차피 값이 다 보이니 필요 없다.
- 프로덕션 로깅/모니터링 도구가 아니다. 순수 개발 도구다.
- day4~6이 런타임 UX를 위한 훅이었다면 이건 순수 DX(개발자 경험) 훅이다. 훅 안에 `console.log`를 심고 싶어질 때 떠올리면 된다. 콘솔 도배 없이 박아두고 잊어도 되는 로그다.
