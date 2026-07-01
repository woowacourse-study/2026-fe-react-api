## useEffectEvent
- Effect는 다시 실행하지 않으면서 Effect 내부에서 항상 최신 반응형 값 (props, state)를 읽기 위해 만들어진 hook 이다 

### 특징
- 최신 반응형 값(props, state) 갱신이 자동으로 이루어진다
- Effect hook(useEffect, useLayoutEffect, useInsertionEffect) 안에서 사용해야 한다
- 즉 다른 hook, 다른 컴포넌트로 전달 불가능
- 참조값이 계속 바뀐다

### 왜, 어떤 문제를 해결하기 위해서 나오게 되었나?
- before
  ```
  function Component({ count }){
    useEffect(() => {
      const id = setInterval(() => {
        console.log(count)
      }, 1000);

      return () => {
        clearInterval(id);
      }
    }, [count]);
  }
  ```

  이 코드는 count가 바뀔때마다 setInterval로 설정한 Timer 가 초기화되어 
  안정적으로 1초마다 console.log가 찍히지는 것을 보장하지 않는다 
  그렇다고 count를 useEffect의 의존성에 넣지 않으면 count 값이 변경되어도 반영이 되지 않는 버그가 발생한다

- before의 문제점을 해결하기 위해 나온 hack
  ```
  // before의 문제점을 해결하기 위해 나온 hack
  function Component({ count }){
    const countRef = useRef(count)
    countRef.current = count;
    useEffect(() => {
      const id = setInterval(() => {
        console.log(countRef.current)
      }, 1000);
    }, []);
  }
  ```
  타이머 setInterval 이 참조하는 값을 ref로 둬서 해결했지만
  이경우 ref 값을 렌더링 중 최신값으로으로 신해야하는 코드를 작성해야하는 문제가 발생한다

- useCallback 으로 해결할 수는 없나?
  ```
  // useCallback 으로 해결할 수는 없나?
  function Component({ count }){
    const timer = useCallback(() => {
      console.log(countRef.current);
    }, [count]);
    
    useEffect(() => {
      const id = setInterval(timer, 1000);
      
      return () => {
        clearInterval(id);
      }
    }, [timer]);
  }
  ```
  - useCallback 은 함수의 참조 안정성을 유지하기 위해 고안된 hook 으로 Effect에서 이 useCallback 함수를 의존성 배열에 어차피 넣어줘야 한다

- useEffectEvent hook을 사용한 이후 코드
  ```
    // useEffectEvent hook을 사용한 이후 코드
    function Component({ count }){
      const timer = useEffectEvent(() => {
        console.log(countRef.current);
      });
      
      useEffect(() => {
        const id = setInterval(timer, 1000);
        
        return () => {
          clearInterval(id);
        }
      }, []);
    }
    ```
  ```

### 어떤 경우에 쓰이나
- 타이머 함수
- event handler 
- 외부 구독시

* Effect 내부에서 사용 전제

### reference
- https://ko.react.dev/reference/react/useEffectEvent
- https://ko.react.dev/learn/separating-events-from-effects#declaring-an-effect-event
- 
- https://chatgpt.com/share/6a452366-24bc-83e8-ac28-652d75b3a0d5
- https://chatgpt.com/share/6a452376-7dec-83e9-8edc-9ba0d31d5967
