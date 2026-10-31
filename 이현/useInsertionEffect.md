## useInsertionEffect

### 요약

런타임에 <style> 태그가 늦게 들어오는 문제를 해결하기 위해서 나온 훅이다.
useEffect에서 주입하면, paint가 되고, 스타일이 주입되어 스타일 적용이 되지 않은 화면이 보일 수 있다.
useLayoutEffect를 사용하면 해결이 되긴 하는데, 만약 다른 곳에서 useLayoutEfect시점에 요소 크기를 써야한다거나 그런 경우에 스타일이 적용되기 전 값을 읽을 수 있다.
그래서 useInsertionEffect를 활용해 <style> 태그를 보다 빠르게 넣어준다.

실행순서는
userInsertionEffect -> useLayoutEffect -> 브라우저 paint -> useEffect

### 주요 용도

CSS-in-JS 라이브러리에서 layoutEffect보다 빠르게 <style> 태그를 주입해야할 때 (이렇게 해야 문제 X)

### 예시

```tsx
function useCSS(rule) {
  useInsertionEffect(() => {
    // ... <style> 태그를 여기에서 주입하세요 ...
  });
  return rule;
}
```
