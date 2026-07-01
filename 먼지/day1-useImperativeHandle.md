## useImperativeHandle

### 효과
- 부모에게 명령형 기능을 제한적으로 공개하는 escape hatch
- 부모가 자식의 메서드나 상태에 접근할 수 있다
  - 하지만 자식의 state, setState 를 노출시키는걸 권장하지는 않고 
    자식의 기능 중 일부분에 대해 API를 공개하고 부모가 호출할수 있도록 열어줌 
- state 조작은 사실 부모가 자식으로 props 형태로 내려주는 일반적인 react 사용 방식 같은게 좋고
  focus와 같이 dom 조작, 행동을 명령하는 케이스에서 사용하는게 좋다
  - 브라우저에게 이 순간 포커스를 줘 라고 명령

### 본질
- 부모가 전달한 ref.current 에 어떤값을 넣을지 자식이 직접 정의하는 hook 이다

### 궁금증
- 부모가 props로 ref를 그냥 넘겨주고 자식이 그냥 변경해도 될것 같은데 useImperativeHandle 이라는 hook 이 별도로 필요한가?
  - 필요하다
  - 1. react도 ref를 관리한다
    <input ref={ref} /> 라고 하면
    react가 렌더링이 끝난 뒤에 ref.current = inputElement 를 넣어준다
    자식도 ref.current = { focus(){} } 넣는다면
    react 와 컴포넌트가 같은 ref를 서로 덮어쓰게 된다
  2. 렌더링 중에는 ref를 변경하면 안된다
    function Child({ref}){
      ref.current = ...
    }
    useImperativeHandle 는 커밋 시점에 ref를 업데이트 하도록 한다
  3. 의존성 관리
    최신 state 를 어떻게 반영할지를 명시적으로 관리한다

- 자식 요소에 대한 dom 조작이라면 기존 ref 사용 형태로도 가능할텐데
  왜 별도의 useImperativeHandler 가 나왔나?
  1. 필요한 기능만 공개하기 위해서
  - useImperativeHandler 를 사용하는 경우 
    자식의 내부 구현이 변경되더라도 부모에서 사용하는 형태는 그대로일 수 있도록 만들수 있다 
  
    before
    ```
      <input />
    ```

    after
    ```
      <div>
        <input />
        <button>X</button>
      </div>
    ```

### reference
- https://ko.react.dev/reference/react/useImperativeHandle
- https://chatgpt.com/share/6a4280d2-81d8-83ee-b39f-812f1b480811
