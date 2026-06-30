## useInsertionEffect

### 요약

일반 컴포넌트 개발용 훅은 아니고, CSS-in-JS 라이브러리 작성자를 위한 특수 훅이다.

DOM 레이아웃을 읽는 효과가 실행되기 전에, <style> 태그 같은 스타일을 먼저 DOM에 삽입하기 위한 훅

실행 타이밍

DOM 변경 -> useInsertionEffect -> useLayoutEffect -> 브라우저 paint -> useEffect

```tsx
useInsertionEffect(() => {
  // CSS-in-JS 라이브러리 내부에서
  // document.head에 <style> 태그 삽입
}, [rule]);
```

정리하면, CSS-in-JS 라이브러리가 스타일을 너무 늦게 넣어서 레이아웃 계산이 틀어지는 문제를 막기 위한 저수준 훅
