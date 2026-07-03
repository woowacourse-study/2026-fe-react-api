## useId

### 키워드

### 요약

어떤 값에 대해서 최신값 이전의 값을 가져오는 훅이다.
useTransition이 상태 업데이트 자체를 지연시키는 것이라면, useDeferredValue는 값 자체를 지연시키는 것이다.

useTransition처럼 덜 중요한 값의 우선순위를 낮추는 용도로 사용한다.
지연시키고자 하는 값을 첫 번째 인자로 넣으면되고 두 번째 인자는 초기값이고 선택적으로 넣어줄 수 있음.

원본 값이 바뀌어도 deferred된 값은 일단 이전 값으로 렌더링 됨.
그리고 원본 값을 따라잡기 위해 deferred된 값을 최신 값으로 업데이트 하는 렌더링을 한 번 더 함.

또 useTransition처럼 원본값을 따라잡기 위한 렌더링 과정중에 원본값이 또 바뀌면 만들던 렌더링 스냅샷 버림.

### 주요 용도

첫 번째는 검색어 입력시 로딩 스피너 대신 이전 결괏값을 표시할 수 있음.
검색어(query) 상태가 존재하고, 이를 useDeferredValue로 감싸면 됨.
그럼 최신 검색 결과를 가져올 때까지 이전 결괏값을 표시할 수 있음.

성능 최적화 용도

### 예시

```tsx
import { useState, useDeferredValue } from "react";
import SlowList from "./SlowList.js";

export default function App() {
  const [text, setText] = useState("");
  const deferredText = useDeferredValue(text);
  return (
    <>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <SlowList text={deferredText} />
    </>
  );
}
```

여기서 SlowList 컴포넌트가 memo로 감싸진 상태임.
이 상황에서는 useDeferredValue로 인한 성능 최적화가 꽤 유의미하다.

만약 useDeferredValue를 쓰지 않는 상황에서 내가 값을 입력하면, 리스트가 모두 렌더링 될 때까지 나는 input에 값을 입력해도 보이지 않는다.
js가 리스트 렌더링을 처리할 때까지 commit하지 않기 때문

하지만 useDeferredValue로 감싸면, 내가 input에 값을 계속해서 입력해도 deferredText는 내가 값 입력을 멈출 때까지는 빈 문자열일 것이기 때문에 리스트를 렌더링하지 않는다. 그래서 input에 내가 입력하는 값이 계속 보인다.
