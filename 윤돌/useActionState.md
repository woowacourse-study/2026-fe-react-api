# useActionState

<br />

## 1. 왜 등장했을까?

Form을 제출한 뒤에는 보통 로딩 상태, 성공 여부, 에러 메시지 등을 여러 개의 상태로 관리해야 한다.

`useActionState`는 이러한 과정을 단순하게 만들기 위해 등장했다.

Action의 반환값을 자동으로 상태로 관리하여 Form의 상태와 결과를 쉽게 다룰 수 있다.

<br />

## 2. 어떤 훅일까?

```tsx
const [state, formAction, isPending] = useActionState(action, initialState);
```

`useActionState`는 Action의 반환값을 상태로 관리하는 Hook이다.

Action이 실행되면 반환값이 새로운 `state`가 되며, 실행 중 여부도 함께 제공한다.

### 매개변수

- **action** : Form 제출 시 실행할 함수
- **initialState** : 초기 상태

```tsx
async function action(previousState, formData) {
  ...
  return nextState;
}
```

- **previousState** : 이전 Action의 결과
- **formData** : Form에 입력된 데이터

### 반환값

- **state** : Action의 반환값
- **formAction** : Form의 `action` 속성에 전달하는 함수
- **isPending** : Action 실행 여부

<br />

## 3. 어떤 상황에서 사용할까?

`useActionState`는 **Form 제출 후 상태를 관리해야 하는 경우** 사용한다.

- 로그인
- 회원가입
- 댓글 작성
- 게시글 작성
- 프로필 수정

```tsx
async function login(
  previousState: LoginState,
  formData: FormData,
): Promise<LoginState> {
  const email = formData.get("email") as string;
  const password = formData.get("password") as string;

  const response = await fetch("/api/login", {
    method: "POST",
    body: JSON.stringify({ email, password }),
  });

  if (response.status === 401) {
    return {
      success: false,
      message: "이메일 또는 비밀번호가 올바르지 않습니다.",
    };
  }

  if (!response.ok) {
    throw new Error("서버 오류");
  }

  return {
    success: true,
    message: "로그인에 성공했습니다.",
  };
}
```

Action의 반환값이 자동으로 새로운 `state`가 된다.

<br />

## 4. 사용할 때 주의할 점

- `action`은 이전 상태(`previousState`)와 `formData`를 매개변수로 받는다.
- 예상 가능한 실패(유효성 검사, 로그인 실패 등)는 `return`으로 새로운 상태를 반환한다.
- 예상하지 못한 오류(서버 오류, 코드 버그 등)는 `throw`하여 Error Boundary에서 처리한다.

<br />

## 한 줄 요약

> **useActionState는 Form Action의 반환값을 상태로 관리하고, 실행 상태까지 함께 관리하는 Hook이다.**
