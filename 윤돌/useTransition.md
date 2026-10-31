# useTransition

<br />

## 1. 왜 등장했을까?

React는 상태가 변경되면 컴포넌트를 다시 렌더링한다.

하지만 모든 상태 업데이트가 동일한 중요도를 가지는 것은 아니다.

예를 들어 검색창에서는 사용자의 입력은 즉시 화면에 반영되어야 하지만, 검색 결과를 렌더링하는 작업은 조금 늦어져도 괜찮다.

React는 어떤 이벤트인지에 따라 우선순위를 결정할 수 있지만,
동일한 이벤트 내에서는 React가 이러한 우선순위를 알 수 없어 모든 상태 업데이트를 동일하게 처리했다.

`useTransition`은 이러한 문제를 해결하기 위해 등장했다.

개발자가 **우선순위가 낮은 상태 업데이트**를 React에게 알려주어, 사용자 입력과 같은 중요한 작업이 먼저 처리될 수 있도록 한다.

<br />

## 2. 어떤 훅일까?

```tsx
const [isPending, startTransition] = useTransition();
```

`useTransition`은 상태 업데이트의 우선순위를 낮춰 React가 나중에 처리할 수 있도록 하는 Hook이다.

Transition으로 표시된 업데이트는 더 중요한 업데이트가 발생하면 중단되거나 다시 시작될 수 있다.

### 반환값

- **isPending** : Transition이 진행 중인지 여부
- **startTransition** : 내부의 상태 업데이트를 Transition으로 표시하는 함수

<br />

## 3. 어떤 상황에서 사용할까?

`useTransition`은 **사용자 입력은 즉시 반영하고, 무거운 UI 업데이트는 나중에 처리하고 싶은 경우** 사용한다.

- 검색 결과 렌더링
- 탭 전환
- 페이지 전환 (네비게이션도 우선순위가 낮은 업데이트)
- 대용량 리스트 렌더링
- 필터링, 정렬 등 오래 걸리는 화면 갱신

```tsx
setInput(value);

startTransition(() => {
  setSearchQuery(value);
});
```

이처럼 입력은 즉시 업데이트하고, 검색 결과처럼 오래 걸리는 작업만 Transition으로 처리하여 사용자 경험을 향상시킬 수 있다.

<br />

## 4. 사용할 때 주의할 점

`useTransition`은 **모든 상태 업데이트를 감싸는 Hook이 아니다.**

- 사용자의 입력처럼 즉시 반영되어야 하는 상태는 Transition으로 처리하면 안 된다.
- Transition은 우선순위가 낮은 UI 업데이트에만 사용한다.
- Transition이 진행 중일 때 더 중요한 업데이트가 발생하면 기존 Transition은 중단되고 새로운 Transition이 시작될 수 있다.
- `startTransition` 내부에서는 상태 업데이트만 수행하는 것이 좋다.

<br />

## 한 줄 요약

> **useTransition은 우선순위가 낮은 상태 업데이트를 React에게 알려 사용자 입력과 같은 중요한 작업을 먼저 처리할 수 있도록 하는 Hook이다.**
