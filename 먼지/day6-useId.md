## useId
- 접근성 attribute에 전달할수 있는 고유 id 생성
- 요컨데 uuid와 같이 고유한 id를 만들어주는 걸 react가 hook 차원에서 지원 해준다는 건가? 
  - 비슷한데 uuid와 목적이 다르다고함
  - 접근성과 ssr을 위해 안전한 id를 만든다고 함
  - useId 는 부모 기반으로 id를 계산해서 서버와 클라이언트에서 같은 id를 얻을수 있다고 함
  - 따라서 서버 렌더링과 클라이언트 초기 렌더링 구조가 같아야만 한다
- 매번 생성되지 않고 처음 생성하고 나면 유지 되는 듯 
- createRoot 의 두번째 먜개변수 {identifierPrefix: ''}를 지정하면 이 값과 concat 해서 만들어 준다 

### reference
- https://ko.react.dev/reference/react/useId
