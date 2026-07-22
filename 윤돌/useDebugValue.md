# useDebugValue

<br />

## 1. 왜 등장했을까?

커스텀 Hook를 사용할 때 내부 상태를 확인하기 어렵다.

`useDebugValue`는 React DevTools에서 커스텀 Hook의 현재 상태를 보기 쉽게 표시하기 위해 등장했다.

<br />

## 2. 어떤 훅일까?

```tsx
useDebugValue(value);
```

`useDebugValue`는 React DevTools에 표시할 디버그 정보를 설정하는 Hook이다.

애플리케이션의 동작에는 영향을 주지 않으며, 개발 환경에서만 사용된다.

### 매개변수

- **value** : DevTools에 표시할 값
- **format** _(선택)_ : 표시할 값을 가공하는 함수

<br />

## 3. 어떤 상황에서 사용할까?

주로 **재사용 가능한 커스텀 Hook를 개발할 때** 사용한다.

```tsx
function useOnlineStatus() {
  const isOnline = ...

  useDebugValue(isOnline ? "Online" : "Offline");

  return isOnline;
}
```

React DevTools에서는 다음과 같이 확인할 수 있다.

```text
useOnlineStatus
└── Online
```

<br />

## 4. 사용할 때 주의할 점

- 일반 컴포넌트에서는 거의 사용할 일이 없다.
- 주로 라이브러리나 공용 커스텀 Hook를 만들 때 사용한다.
- 성능이 걱정된다면 `format` 함수를 사용하여 DevTools가 열렸을 때만 값을 계산할 수 있다.

<br />

## 한 줄 요약

> **useDebugValue는 React DevTools에서 커스텀 Hook의 상태를 보기 쉽게 표시하기 위한 Hook이다.**
