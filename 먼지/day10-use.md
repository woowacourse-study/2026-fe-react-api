## use
- Promise나 Context를 참조하려면 use를 사용
- if 문 같은 조건문에서 호출 가능

- Promise
  - Suspense 와 ErrorBoundary 와 통합된다
  - pending 상태일 때 Suspend 상태로 만들어(throw promise 하여) 현재 렌더링을 중단 시킨다 
    resolve 되면 return 값으로 resolve 결과 값이 나오는데 이때 리액트가 렌더링 시킨다
  - Suspense으로 감싸면 fallback으로 화면 렌더링이 된다
  - resolve 되면 처음부터 다시 실행됨  

  - Suspend로 안 감싸면?
    - 최초 렌더링
      - return undefined과 유사 
    - 이미 화면이 있는 상태에서 업데이트 중 suspend
      - 기존 화면을 유지하고 한다고 함

- Context를 넘겨도 된다고 함
  - useContext 대신 사용 가능 

- 어떤 문제를 해결 하기 위해 나온 hook 인가?
  - 비동기 데이터를 React 렌더링 모델 안에서 자연스럽게 읽게 하기 위해서 
  - react의 동시성 모드와 잘 맞는다
  - use는 Promise를 React scheduler 에게 알려주는 역할
  - 서버 컴포넌트와도 잘 맞는다
    - 서버에서 Promise 발생 -> 클라이언트에서 읽기

- mount 시에만 가능한가? 클릭 이벤트를 통한 api 호출과는 상관이 없나?
  - mount 시라고 하기보다 렌더링 중 필요한  데이터를 읽는 경우 사용할 수 있다

### reference
- https://ko.react.dev/reference/react/use
