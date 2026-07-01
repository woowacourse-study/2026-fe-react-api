# useImperativeHandle

<br />

## 1. 왜 등장했을까?

부모 컴포넌트는 `ref`를 통해 자식의 DOM이나 내부 객체에 직접 접근할 수 있다.
하지만 부모가 자식의 내부 구현을 모두 알게 되면, DOM 구조나 구현 방식에 의존하게 되어 컴포넌트 간 결합도가 높아지고 유지보수가 어려워질 수 있다.

```tsx
// 부모는 input의 모든 기능과 속성에 직접 접근할 수 있다.

function MyInput({ ref }) {
  return <input ref={ref} />;
}
```

`useImperativeHandle`은 이러한 문제를 해결하기 위해 등장했다. 부모에게 자식의 실제 DOM이나 내부 구현을 그대로 노출하는 대신, **필요한 기능만 인터페이스 형태로 공개**할 수 있다.
이를 통해 **내부 구현은 숨기고(캡슐화), 부모는 공개된 기능만 사용할 수 있도록 추상화**하여 컴포넌트 간 결합도를 낮출 수 있다.

<br />

## 2. 어떤 훅일까?

```tsx
useImperativeHandle(ref, createHandle, dependencies?)
```

`useImperativeHandle`은 부모 컴포넌트가 `ref`를 통해 접근할 수 있는 **인터페이스를 직접 정의하는 Hook**이다. 자식의 실제 DOM이나 내부 객체를 그대로 노출하는 대신, 필요한 메서드나 값만 선택적으로 공개할 수 있다.

```tsx
// 부모는 공개된 인터페이스만 사용할 수 있다.

function MyInput({ ref }) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => ({
    focus() {
      inputRef.current.focus();
    },
    clear() {
      inputRef.current.value = "";
    },
  }));

  return <input ref={inputRef} />;
}
```

### 매개변수

- **ref** : 부모로부터 전달받은 `ref`
- **createHandle** : 부모에게 노출할 객체(인터페이스)를 반환하는 함수
- **dependencies?** : 의존성 배열. 값이 변경되면 새로운 인터페이스를 생성한다.

### 반환값

- **반환값 없음 (`undefined`)**

<br />

## 3. 어떤 상황에서 사용할까?

`useImperativeHandle`은 부모가 `ref`로 자식에게 접근해야 하지만,
자식의 내부 구현을 그대로 노출하고 싶지 않을 때 사용한다.

- 자식의 DOM 전체가 아니라 필요한 메서드만 공개하고 싶을 때
- 여러 내부 ref를 하나의 인터페이스로 묶어 부모에게 제공하고 싶을 때
- `focus`, `scrollIntoView`, `select`처럼 Props로 표현하기 어려운 명령형 동작을 노출해야 할 때

즉, 단순히 ref를 전달하는 것이 아니라 **부모가 사용할 수 있는 기능을 제한하고 싶을 때** 사용한다.

<br />

## 4. 사용할 때 주의할 점

### Props로 표현할 수 있는 것은 `useImperativeHandle`을 사용하지 않는다.

`useImperativeHandle`은 `focus`, `scrollIntoView`, `select`처럼 **Props만으로 표현하기 어려운 명령형 동작**을 외부에 노출할 때 사용한다.

반대로 화면의 상태를 표현하는 경우에는 `useImperativeHandle`보다 **Props를 통한 선언적인 방식**을 사용하는 것이 좋다.

```tsx
// ❌ 명령형
modalRef.current.open();

// ✅ 선언형
<Modal isOpen={isOpen} />;
```

<br />

## 한 줄 요약

> **useImperativeHandle은 부모가 ref를 통해 사용할 기능만 선택적으로 공개하기 위한 Hook이다.**
