# useId

<br />

## 1. 왜 등장했을까?

컴포넌트 내부에서 고유한 ID가 필요한 경우가 있다.

기존에는 `Math.random()`이나 직접 문자열을 작성해 사용했지만, 여러 컴포넌트를 렌더링하면 ID가 중복될 수 있고, SSR 환경에서는 서버와 클라이언트가 서로 다른 ID를 생성하여 Hydration 문제가 발생할 수 있다.

`useId`는 이러한 문제를 해결하기 위해 등장했다.

<br />

## 2. 어떤 훅일까?

```tsx
const id = useId();
```

`useId`는 접근성을 위한 고유한 ID를 생성하는 Hook이다.

SSR 환경에서도 서버와 클라이언트에서 동일한 ID를 생성하도록 보장한다.

### 반환값

- **id** : 고유한 문자열 ID

<br />

## 3. 어떤 상황에서 사용할까?

`useId`는 **접근성을 위해 HTML 요소를 연결해야 하는 경우** 사용한다.

- `label`과 `input` 연결
- `aria-describedby`
- `aria-labelledby`

```tsx
const id = useId();

<label htmlFor={id}>이메일</label>
<input id={id} />
```

하나의 ID를 기반으로 여러 관련 요소를 생성할 수도 있다.

```tsx
const id = useId();

<input id={`${id}-firstName`} />
<input id={`${id}-lastName`} />
```

<br />

## 4. 사용할 때 주의할 점

- `useId`는 접근성을 위한 ID를 생성하는 Hook이다.
- 리스트의 `key`를 생성하는 용도로 사용하면 안 된다.
- 가능한 경우 데이터 자체의 고유 ID를 사용하는 것이 좋다.

<br />

## 한 줄 요약

> **useId는 접근성을 위한 고유한 ID를 생성하고, SSR 환경에서도 동일한 ID를 보장하기 위한 Hook이다.**
