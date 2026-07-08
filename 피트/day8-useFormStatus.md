## useFormStatus

공식 문서의 표현

> `useFormStatus`는 마지막 폼 제출의 상태 정보를 제공하는 Hook입니다.

### 코드

```ts
const { pending, data, method, action } = useFormStatus();
```

#### 반환값

1. `pending`: 폼 제출 여부 플래그 (boolean)

2. `data`: `FormData` 인터페이스를 구현한 객체, `<form>`이 제출하려는 데이터를 포함한다.

3. `method`: `'get'` 또는 `'post'` 중 하나의 문자열 값

4. `action`: 상위 `<form>`의 `action` Prop에 전달한 함수의 레퍼런스

상태 정보를 제공하므로 form의 대기 상태, form 데이터 관리 등을 수행하는 것을 목적으로 한다.

### 예시

```ts
import { useFormStatus } from 'react-dom';
import { submitForm } from './actions.js';

function Submit() {
  const { pending } = useFormStatus();
  return (
    <button type="submit" disabled={pending}>
      {pending ? 'Submitting...' : 'Submit'}
    </button>
  );
}

function Form({ action }) {
  return (
    <form action={action}>
      <Submit />
    </form>
  );
}

export default function App() {
  return <Form action={submitForm} />;
}
```

대기 상태를 표현하기 위해서 form 태그 내에서 `useFormStatus` 훅을 호출하고, `pending`을 통해 제출 중임을 확인할 수 있다.

중요한 점은 `useFormStatus` 훅이 부모 form의 제출 상태를 읽기 때문에, Submit 컴포넌트에서 호출해도 부모인 Form 컴포넌트의 form 상태를 읽는다.

```ts
import { useState, useMemo, useRef } from 'react';
import { useFormStatus } from 'react-dom';

export default function UsernameForm() {
  const { pending, data } = useFormStatus();

  return (
    <div>
      <h3>Request a Username: </h3>
      <input type="text" name="username" disabled={pending} />
      <button type="submit" disabled={pending}>
        Submit
      </button>
      <br />
      <p>{data ? `Requesting ${data?.get('username')}...` : ''}</p>
    </div>
  );
}
```

form 제출 시 제출 당시의 data가 Formdata 객체에 들어온다. 그래서 위 코드에서 사용자가 입력한 `username` 값을 꺼내서 보여줄 수 있다.

### 결론

`useActionState`와 비슷한 역할을 수행하는 것 같지만 다르다.` useActionState` 훅은 action 실행 후 state로 저장하는 역할을 수행하고, `useFormStatus` 훅은 form 제출 상태를 확인하고 제출 중에 데이터에 접근할 수 있다.

form 제출 중임을 명확하게 보여줄 수 있다는 장점이 있지만, form 태그를 사용하는 하위 컴포넌트에서 사용해야 한다는 점이 단점이지 않을까? 생각이 든다.
