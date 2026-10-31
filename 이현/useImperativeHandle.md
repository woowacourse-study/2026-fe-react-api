## useImperativeHandle

### 키워드

인터페이스, 명령형 로직 숨기기

### 요약

부모 컴포넌트에서 어떤 행동으로, 자식 컴포넌트에 존재하는 html 태그의 dom api를 써야한다면 일반적으로는 부모 컴포넌트에서 그 핸들러를 정의한다.
하지만 자식 요소를 다루는 것을 부모 컴포넌트에서 정의하면 부모 컴포넌트는 자식의 DOM구조를 알아야 한다.
useImperativeHandle 훅을 사용하면 자식 컴포넌트의 내부 ref를 이용해 DOM API를 조작하는 메서드를 정의하고, 부모로부터 전달받은 ref를 통해 해당 메서드만 외부에 공개할 수 있다.
기본 dom api만 훅 내에서 정의할 수 있는게 아니라 사용자 정의 메서드로 만들 수도 있다. 따라서 부모 컴포넌트는 ref와 관련된 인터페이스만 받아서 자식의 DOM 구조를 모른 채로 어떻게 행동할지만 알면 된다.

### 주요 용도

부모 컴포넌트에서 ref로 자식 컴포넌트의 dom에 접근해 dom api를 사용해야될 때 사용된다.

### 예시

```tsx
function MyInput({ ref }) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      focus() {
        inputRef.current.focus();
      },
      scrollIntoView() {
        inputRef.current.scrollIntoView();
      },
    };
  }, []);

  return <input ref={inputRef} />;
}
```
