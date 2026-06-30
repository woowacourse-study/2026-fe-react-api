## useInsertionEffect

### 요약

`useInsertionEffect`는 React가 DOM을 변경한 직후, 브라우저가 화면을 그리거나 `useLayoutEffect`가 실행되기 전에 동작하는 특수한 훅으로, 주로 CSS-in-JS 라이브러리에서 동적으로 `<style>` 태그를 삽입할 때 사용한다. 일반적인 데이터 요청이나 이벤트 처리에는 `useEffect`, DOM 크기 측정이나 레이아웃 조정에는 `useLayoutEffect`를 쓰면 되고, `useInsertionEffect`는 스타일이 늦게 적용되어 레이아웃 계산이 틀어지는 문제를 막기 위한 저수준 API라서 일반 앱 코드에서는 거의 직접 사용할 일이 없다.

### 실행 타이밍

DOM 변경 -> useInsertionEffect -> useLayoutEffect -> 브라우저 paint -> useEffect

### 예시

```tsx
useInsertionEffect(() => {
  // CSS-in-JS 라이브러리 내부에서
  // document.head에 <style> 태그 삽입
}, [rule]);
```

정리하면, CSS-in-JS 라이브러리가 스타일을 너무 늦게 넣어서 레이아웃 계산이 틀어지는 문제를 막기 위한 저수준 훅
