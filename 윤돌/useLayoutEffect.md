# useLayoutEffect

<br />

## 1. 왜 등장했을까?

`useEffect`는 브라우저가 화면을 그린(Paint) 이후 실행된다.

대부분의 작업은 문제가 없지만, Tooltip처럼 **DOM의 크기나 위치를 측정한 뒤 화면을 다시 수정해야 하는 경우**에는 화면이 한 번 잘못 그려졌다가 다시 그려지면서 깜빡임이 발생할 수 있다.

`useLayoutEffect`는 이러한 문제를 해결하기 위해 등장했다. 브라우저가 화면을 그리기 전에 Effect를 실행하여 DOM을 측정하거나 수정할 수 있으므로, 사용자는 최종 결과만 보게 된다.

<br />

## 2. 어떤 훅일까?

```tsx
useLayoutEffect(setup, dependencies?)
```

`useLayoutEffect`는 브라우저가 화면을 그리기(Paint) 전에 실행되는 Effect Hook이다. 주로 DOM의 크기나 위치를 측정하거나, 화면이 그려지기 전에 레이아웃을 수정해야 하는 경우 사용한다.

### 매개변수

- **setup** : 실행할 Effect 함수
- **dependencies?** : Effect가 다시 실행될 의존성 배열

### 반환값

- **반환값 없음 (`undefined`)**

<br />

## 3. 어떤 상황에서 사용할까?

`useLayoutEffect`는 **화면이 그려지기 전에 DOM을 읽거나 수정해야 하는 경우** 사용한다.

- Tooltip이나 Popover의 위치를 계산할 때
- DOM의 크기나 위치를 측정할 때
- 화면이 그려지기 전에 스크롤 위치를 조정할 때
- 깜빡임(Flicker) 없이 레이아웃을 수정해야 할 때

대부분의 경우에는 `useEffect`를 사용하는 것이 적합하며, 화면이 먼저 그려지면 안 되는 경우에만 `useLayoutEffect`를 사용한다.

```tsx
useLayoutEffect(() => {
  const { height } = ref.current.getBoundingClientRect();
  setTooltipHeight(height);
}, []);
```

<br />

## 4. 사용할 때 주의할 점

`useLayoutEffect`는 브라우저의 Paint를 막고 실행되므로 성능에 영향을 줄 수 있다.

- 가능하면 `useEffect`를 먼저 고려한다.
- DOM을 측정하고 수정해야 하는 경우에만 사용한다.
- 불필요한 연산이나 오래 걸리는 작업은 피한다.

<br />

## 한 줄 요약

> **useLayoutEffect는 화면이 그려지기 전에 Effect에서 필요한 UI 작업을 먼저 수행하기 위한 Hook이다.**
