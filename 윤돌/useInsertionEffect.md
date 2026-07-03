# useInsertionEffect

<br />

## 1. 왜 등장했을까?

CSS-in-JS 라이브러리는 컴포넌트가 렌더링될 때 동적으로 CSS를 생성하여 `<style>` 태그를 삽입한다.

하지만 스타일이 너무 늦게 삽입되면, 브라우저가 스타일이 적용되지 않은 상태로 레이아웃을 계산하거나 화면을 그린 뒤 다시 계산해야 할 수 있다.

`useInsertionEffect`는 이러한 문제를 해결하기 위해 등장했다. 브라우저가 레이아웃을 계산하기 전에 스타일을 먼저 삽입하여, 올바른 스타일을 기준으로 화면을 그릴 수 있도록 한다.

<br />

## 2. 어떤 훅일까?

```tsx
useInsertionEffect(setup, dependencies?)
```

`useInsertionEffect`는 브라우저가 레이아웃을 계산하기 전에 실행되는 Effect Hook이다. 주로 CSS-in-JS 라이브러리에서 동적으로 생성한 스타일을 삽입하기 위해 사용한다.

### 매개변수

- **setup** : 실행할 Effect 함수
- **dependencies?** : Effect가 다시 실행될 의존성 배열

### 반환값

- **반환값 없음 (`undefined`)**

<br />

## 3. 어떤 상황에서 사용할까?

`useInsertionEffect`는 **CSS-in-JS 라이브러리를 구현할 때** 사용한다.

- 동적으로 생성한 CSS를 `<style>` 태그에 삽입할 때
- 레이아웃 계산 전에 스타일이 적용되어야 할 때

```tsx
useInsertionEffect(() => {
  // <style> 태그에 CSS 삽입
});
```

일반적인 React 애플리케이션에서는 거의 사용할 일이 없으며, 대부분 `useEffect`나 `useLayoutEffect`를 사용하는 것이 적합하다.

<br />

## 4. 사용할 때 주의할 점

`useInsertionEffect`는 **라이브러리 제작자를 위한 Hook**이다.

- 일반적인 데이터 요청이나 DOM 조작에는 사용하지 않는다.
- CSS-in-JS처럼 스타일을 삽입하는 경우에만 사용한다.
- 대부분의 애플리케이션에서는 `useEffect` 또는 `useLayoutEffect`를 사용하면 충분하다.

<br />

## 한 줄 요약

> **useInsertionEffect는 CSS-in-JS 라이브러리가 스타일을 레이아웃 계산 전에 삽입하기 위한 Hook이다.**
