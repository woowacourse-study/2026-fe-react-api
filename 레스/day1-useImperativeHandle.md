## useImperativeHandle

### 요약

useImperativeHandle은 이름 그대로 부모가 ref를 통해 호출할 수 있는 명령형 핸들을 직접 정의하는 훅이다.
즉, 부모 컴포넌트가 `ref`를 통해서 자식 컴포넌트에 접근할 때, 실제 DOM 노드 전체를 그대로 노출하지 않고 자식이 직접 정한 값이나 메서드만 노출하게 해주는 훅이다.

보통 리액트에서는 props와 state로 UI를 제어하는 선언적인 방식이 기본이다. 하지만 포커스 이동, 스크롤 이동, 텍스트 선택처럼 특정 DOM에 직접 명령을 내려야 하는 상황이 있다.
이때 부모가 자식 내부 DOM을 직접 만지는 대신에 자식이 `focus`, `scrollIntoView` 같은 필요한 메서드만 열어줄 수 있다.

즉, `useImperativeHandle`은 자식 컴포넌트의 내부 구현을 숨기면서 부모에게 필요한 명령형 API만 제공하는 도구라고 볼 수 있다.

### 주요 용도

`useImperativeHandle`은 부모가 자식 컴포넌트에 직접 명령을 내려야 하는 예외적인 상황에서 사용한다.

대표적으로 input에 포커스를 주거나, 특정 요소로 스크롤을 이동하거나, 텍스트를 선택하거나, 외부 라이브러리로 만든 컴포넌트의 메서드를 부모에서 실행해야 할 때 사용할 수 있다.

다만 자주 쓰는 훅은 아닌 것 같다. 모달을 열고 닫는 것처럼 상태로 표현할 수 있는 동작은 `ref.current.open()` 같은 방식보다 `isOpen` props로 제어하는 편이 React에 더 자연스러워보인다.
`useImperativeHandle`은 props와 state로 표현하기 어려운 명령형 동작에 제한적으로 사용하는 것이 좋다.


#### useImperativeHandle(ref, createHandle, dependencies?)

인자에 ref, 콜백, 의존성배열이 들어간다.

1. `ref`는 부모가 자식 컴포넌트에 넘겨준 `ref`이고, 최종적으로 부모가 `ref.current`로 접근하게 되는 통로이다.
2. `createHandle`은 그 `ref.current`에 들어갈 값을 만들어 반환하는 함수다. 예를 들어 `() => ({ focus() { inputRef.current?.focus(); } })`를 넘기면 부모는 `ref.current.focus()`만 호출할 수 있다. 즉, 자식이 허용한 메서드만 부모에게 노출하는 것이다.
3. 마지막 `dependencies`는 `createHandle` 안에서 사용하는 `props`, `state` 같은 값들의 목록이다. 이 값들이 바뀌면 리액트가 핸들을 다시 만들어 최신 값이 반영되게 한다.

정리하면 `ref`는 부모와 연결되는 통로, `createHandle`은 그 통로로 내보낼 API를 만드는 콜백, `dependencies`는 그 API를 언제 다시 만들지 정하는 기준이다.
