# useSyncExternalStore

<br />

## 1. 왜 등장했을까?

React는 `useState`나 `useContext`처럼 **React 내부의 상태**는 직접 관리할 수 있다.

하지만 `localStorage`, `navigator.onLine`, Redux, Zustand처럼 **React 외부의 저장소**는 언제 값이 변경되는지 알지 못한다.

기존에는 `useEffect`를 사용하여 외부 저장소를 구독하고, 변경된 값을 다시 React state로 복사하여 관리해야 했다.

하지만 이 방식은 외부 저장소와 React state라는 **두 개의 상태를 유지**해야 하며, Concurrent Rendering 환경에서는 컴포넌트마다 서로 다른 값을 읽는 문제가 발생할 수 있다. (렌더링 중 외부 저장소가 바뀔 수 있지만, React는 이를 모른다.)

`useSyncExternalStore`는 이러한 문제를 해결하기 위해 등장했다.

외부 저장소를 직접 구독하고 최신 Snapshot을 읽어 React와 동기화할 수 있는 공식적인 방법을 제공한다.

<br />

## 2. 어떤 훅일까?

```tsx
const snapshot = useSyncExternalStore(subscribe, getSnapshot);
```

`useSyncExternalStore`는 React 외부의 저장소를 구독하고 현재 Snapshot을 읽기 위한 Hook이다.

외부 저장소가 변경되면 React는 `getSnapshot`을 다시 호출하여 이전 Snapshot과 비교하고, 값이 변경된 경우에만 리렌더링한다.

### 매개변수

- **subscribe** : 외부 저장소를 구독하고, 구독을 해제하는 함수를 반환한다.
- **getSnapshot** : 현재 저장소의 Snapshot을 반환한다.
- **getServerSnapshot?** : 서버 렌더링 시 사용할 초기 Snapshot을 반환한다.

### 반환값

- **현재 Snapshot**

<br />

## 3. 어떤 상황에서 사용할까?

`useSyncExternalStore`는 **React 외부에서 관리되는 값을 여러 컴포넌트가 함께 사용해야 하는 경우** 사용한다.

- Redux, Zustand 같은 외부 Store
- `localStorage`
- `navigator.onLine`
- `window.matchMedia`
- 브라우저나 외부 라이브러리에서 관리하는 상태

기존에는 `useEffect`를 사용하여 외부 저장소를 React state로 복사해야 했다.

```tsx
const [isOnline, setIsOnline] = useState(navigator.onLine);

useEffect(() => {
  function handleChange() {
    setIsOnline(navigator.onLine);
  }

  window.addEventListener("online", handleChange);
  window.addEventListener("offline", handleChange);

  return () => {
    window.removeEventListener("online", handleChange);
    window.removeEventListener("offline", handleChange);
  };
}, []);
```

`useSyncExternalStore`를 사용하면 React가 외부 저장소를 직접 구독하고 최신 Snapshot을 읽는다.

```tsx
const isOnline = useSyncExternalStore(subscribe, getSnapshot);
```

```tsx
function subscribe(callback: () => void) {
  window.addEventListener("online", callback);
  window.addEventListener("offline", callback);

  return () => {
    window.removeEventListener("online", callback);
    window.removeEventListener("offline", callback);
  };
}

function getSnapshot() {
  return navigator.onLine;
}
```

읽기(동기화)는 `useSyncExternalStore`가 담당하고, 저장소의 변경은 Store나 도메인 함수가 담당하는 것이 일반적인 구조이다.

<br />

## 4. 사용할 때 주의할 점

`useSyncExternalStore`는 **외부 저장소를 읽고 구독하기 위한 Hook**이다.

- `getSnapshot`은 값이 변경되지 않았다면 항상 동일한 값을 반환해야 한다.
- `subscribe`는 저장소 변경을 React에게 알리고, 구독 해제 함수를 반환해야 한다.
- 저장소를 어떻게 변경할지는 강제하지 않는다. 대신 저장소를 읽고 구독하는 방식을 표준화한다. (하나의 통로로 모은다.)

<br />

## 한 줄 요약

> **useSyncExternalStore는 React 외부의 저장소를 직접 구독하고 최신 Snapshot을 읽어 React와 동기화하기 위한 Hook이다.**
