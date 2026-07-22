## useImperativeHandle

공식 문서의 표현

> `useImperativeHandle`은 Ref로 노출되는 핸들을 사용자가 직접 정의할 수 있게 해주는 React Hook입니다.

### 코드

```ts
useImperativeHandle(ref, createHandle, dependencies?)
```

#### 매개변수

`ref`: ref.

`createHandle`: 수가 없고 노출하려는 Ref 핸들을 반환하는 메서드. 어떠한 유형이든 될 수 있지만, 일반적으로 노출하려는 메서드가 있는 객체를 반환함.

`useImperativeHandle` 훅은 Ref 핸들러로 자식 컴포넌트의 특정 기능을 직접 호출할 수 있게 해주는 역할을 수행한다.

`Ref`는 원래 부모가 자식의 DOM 요소에 접근할 때 사용한다. 그런데 `useImperativeHandle` 훅을 사용하면 부모에게 DOM 요소 전체를 주는 대신, 커스텀된 메서드만 존재하는 객체를 줄 수 있다.

### 그냥 Ref를 전달하는 경우

```ts
function MyInput({ ref }) {
  return <input ref={ref} />;
}

function Parent() {
  const inputRef = useRef<HTMLInputElement>(null);

  return <MyInput ref={inputRef} />;
}
```

이 경우에 부모가 받는 것은 실제 `<input>` DOM 노드이다.

### 훅을 사용하는 경우

```ts
import { useImperativeHandle, useRef } from 'react';

function MyInput({ ref }) {
  const inputRef = useRef<HTMLInputElement>(null);

  useImperativeHandle(
    ref,
    () => {
      return {
        focus() {
          inputRef.current?.focus();
        },
        scrollIntoView() {
          inputRef.current?.scrollIntoView();
        },
      };
    },
    [],
  );

  return <input ref={inputRef} />;
}
```

부모에게 받은 `ref`를 그냥 사용하지 않고, (자식) 내부에서 사용하는 `inputRef`를 노출시킨다.
따라서 부모는 아래 객체만 알고 있게 된다.

```ts
{
  focus() {
    inputRef.current?.focus();
  },
  scrollIntoView() {
    inputRef.current?.scrollIntoView();
  },
}
```

`inputRef.current`가 더 이상 `HTMLInputElement`가 아니기 때문에, 임의로 `value`를 변경한다거나 `disabled` 값을 변경하는 등의 행동을 할 수 없다.

물론 위 코드 예시에서처럼 focus, scrollIntoView와 같은 이미 있는 메서드명으로 강제할 필요는 없다. 커스텀한 메서드를 공개시켜줄 수 있다.

### 결론

`useImperativeHandle` 훅은 자식 컴포넌트가 부모에게 DOM 요소 전체는 숨기고, 공개할 기능들만 사용하라고 전달하는 훅이다.

컴포넌트 내부 구현은 숨기고, 필요한 명령형 동작만 노출시킬 수 있다.
