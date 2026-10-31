## useDebugValue
- react dev tool 에 라벨을 추가할수 있개 해주는 hook
- 문법 
  ```
    useDebugValue(value, format?);
  ```
- 사용 예시
  ```
    function useOnlineStatus(){
      const isOnline = useSyncExternalStore(...);
      useDebugValue(isOnline ? 'online': 'offline');

      return isOnline;
    }
  ```
  - isOnline 값이 어떤지 react dev tool 에서 확인 가능
  - isOnline 이 true 일때 표시만 'online' 으로 뜨는 것
  - 즉 react dev tool 에서 true 면 'online', false 면 'offline' 으로 보여줘
  
- 내부 구조가 복잡해서 디버깅 하기 어려운 hook 일때 사용
- production build 시에도 포함됨
- useFormValues 를 기반으로 했을때 예시
  ```
    function useFormValues(){
      useDebugger({
        values,
        blurs,
        refs,
        errors,
        isValid,
      })
    }
  ```

### reference
- https://ko.react.dev/reference/react/useDebugValue
- https://chatgpt.com/share/6a4d12f2-e1c4-83e8-bcd3-071d54fd00fc