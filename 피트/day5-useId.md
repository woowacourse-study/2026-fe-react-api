## useId

공식 문서의 표현

> `useId`는 접근성 어트리뷰트에 전달할 수 있는 고유 ID를 생성하기 위한 React Hook입니다.

### 코드

```ts
const id = useId();
```

매개변수는 없고, `useId`를 호출한 특정 컴포넌트와 특정 `useId`에 관련된 고유 ID 문자열을 반환한다.

고유 ID란?

- 우리가 컴포넌트의 ID로 전달하는 그 ID 맞다. 주로 label이나 input에 전달한다.
- 태그의 ID를 직접 입력하는 것은 좋은 사례가 아니다. 컴포넌트는 계속 렌더링될 수 있지만 ID는 고유해야 한다.

`useId`가 SSR 환경에서 좋다고 한다. 왜일까?

- 우선 SSR 환경은 다음과 같이 동작한다.

  1. 서버에서 React 컴포넌트를 HTML로 렌더링한다.
  2. 브라우저가 그 HTML을 먼저 보여준다.
  3. 클라이언트 React가 같은 컴포넌트를 다시 실행하면서 hydration한다.
     이 때 서버의 HTML ID와 클라이언트가 하이드레이션할 때 만든 ID가 일치해야 한다.
     `useId`를 사용하지 않고 전역 카운터를 만들어보자.

```ts
let nextId = 0;

function InputField() {
  const id = `input-${nextId++}`;

  return (
    <>
      <label htmlFor={id}>이메일</label>
      <input id={id} />
    </>
  );
}
```

서버에서는 input-0, input-1 순서로 렌더링되어도, 클라이언트 하이드레이션에서는 컴포넌트가 항상 서버와 같은 순서로 처리된다고 보장하기 어렵다. 따라서 hydration mismatch가 생길 수 있다.

`useId`는 이 문제를 줄이기 위해 React가 컴포넌트 트리의 위치를 기준으로 안정적인 ID를 만들어준다.
