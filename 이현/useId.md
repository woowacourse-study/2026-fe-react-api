## useId

### 키워드

접근성, 서버 사이드 렌더링

### 요약

`aria-describedby`같은 접근성 어트리뷰트에 전달할 수 있는 고유 ID를 생성하기 위한 훅

- 매개변수 X
- useId를 리스트의 key를 생성하기 위해 사용하면 안 된다.

html에서 쓰는 것처럼 id를 React 컴포넌트 내에서 직접 지정할 수 있음. 하지만 동일한 컴포넌트를 여러개 사용한다면, 하드코딩한 id는 더이상 고유한 값이 아님.

동일한 컴포넌트를 여러 개 렌더링해도 useId는 각 컴포넌트 인스턴스에 서로 다른 ID를 만들어 넣어주고, 같은 인스턴스의 리렌더링 사이에서는 동일한 ID를 유지시켜준다.

### 주요 용도

접근성을 위해 서로 다른 태그를 연결하기 위함

### 예시

```tsx
const passwordHintId = useId();

return (
  <>
    <input type="password" aria-describedby={passwordHintId} />
    <p id={passwordHintId}>비밀번호 안내</p>
  </>
);
```
