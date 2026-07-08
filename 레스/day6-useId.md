## useId

### 나온 배경

접근성 속성용 고유 ID를 만들어주는 Hook. React 18부터 정식 API.

`label`의 `htmlFor`와 `input`의 `id`를 연결하려면 id가 필요한데, `id="password-input"`처럼 하드코딩하면 그 컴포넌트가 한 페이지에 두 번 렌더링되는 순간 id가 충돌한다. label을 클릭하면 엉뚱한 input에 포커스가 간다.

그럼 `Math.random()`이나 카운터로 만들면 되지 않나 싶지만 SSR에서 깨진다. 서버가 만든 HTML의 id와 클라이언트가 하이드레이션하면서 만든 id가 달라져서 hydration mismatch가 터진다.

### 요약

```jsx
const id = useId();
```

- 컴포넌트 인스턴스마다 고유하고 서버와 클라이언트에서 **같은 값이 나오는** ID 문자열을 돌려준다
- 고유성의 단위는 인스턴스 × 호출 위치. 같은 컴포넌트를 세 번 렌더링하면 세 개의 다른 id가 나온다
- 용도는 `htmlFor`↔`id`, `aria-describedby` 같은 HTML 속성끼리 짝지어주는 것

### 예시

```jsx
function PasswordField() {
  const passwordHintId = useId();
  return (
    <>
      <label>
        비밀번호:
        <input type="password" aria-describedby={passwordHintId} />
      </label>
      <p id={passwordHintId}>비밀번호는 18자 이상이어야 합니다.</p>
    </>
  );
}
```

이 컴포넌트를 페이지에 몇 개를 놓든 각자 다른 id를 받아서 연결이 안 꼬인다.

id가 여러 개 필요하면 useId를 여러 번 부르지 말고 하나 받아서 접미사를 붙이는 게 권장 패턴이다.

```jsx
function Form() {
  const id = useId();
  return (
    <form>
      <label htmlFor={id + '-firstName'}>이름:</label>
      <input id={id + '-firstName'} />
      <label htmlFor={id + '-lastName'}>성:</label>
      <input id={id + '-lastName'} />
    </form>
  );
}
```

### 왜 카운터로는 안 되나

useId는 id를 순서가 아니라 **트리에서의 위치(부모 경로)**로 만든다. "루트의 2번째 자식 → 그 안의 1번째 자식" 같은 좌표를 인코딩한 게 `:r2m:` 같은 문자열이다.

카운터는 렌더링 순서에 의존하는데, React 18의 스트리밍 SSR에서는 Suspense 경계별로 HTML이 순서 없이 도착하고 하이드레이션도 사용자가 먼저 건드린 부분부터 일어난다. 서버에서 5번째로 렌더링된 컴포넌트가 클라이언트에선 2번째로 하이드레이션될 수 있다는 뜻이다. 트리 좌표는 순서와 무관하니 서버와 클라이언트가 같은 트리를 만들기만 하면 id가 일치한다.

뒤집으면 제약도 나온다. 서버와 클라이언트의 트리가 다르면(`typeof window` 분기 등) id도 같이 어긋난다.

### 주의사항

- 리스트의 `key`로 쓰면 안 된다. key는 데이터에서 나와야 하고 useId는 트리 위치에서 나온다.
- 생성된 id에 `:`가 들어가서(`:r0:`) CSS 셀렉터로 못 쓴다. `querySelector('#:r0:')`는 실패한다. "이 id로 DOM 조작하지 마라"는 의도적 설계다.
- 한 페이지에 React 앱이 여러 개면 `createRoot(container, { identifierPrefix: 'my-app-' })`로 충돌을 피한다. SSR이면 서버(`renderToPipeableStream`)와 클라이언트(`hydrateRoot`) 양쪽에 같은 접두사를 줘야 한다.
