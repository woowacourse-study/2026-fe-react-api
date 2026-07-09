## useSyncExternalStore

공식 문서의 표현

> `useSyncExternalStore`는 외부 store를 구독할 수 있는 React Hook입니다.

### 코드

```ts
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)
```

#### 매개변수

1. `subscribe`: 하나의 `callback` 인수를 받아 store를 구독하는 함수 (구독을 정리하는 함수를 반환해야 함)
2. `getSnapshot`: 컴포넌트에 필요한 store 데이터의 스냅샷을 반환하는 함수

#### 반환값

`snapshot`: 렌더링 로직에 사용할 수 있는 store의 현재 스냅샷

외부 저장소를 구독하는 역할을 수행한다.

더 자세히 말하면, props, state, context 말고 외부의 저장소에서 데이터를 읽어야 하는 상황에서 `useSyncExternalStore` 훅을 사용할 수 있다.

### 예시

```ts
import { useSyncExternalStore } from 'react';
import { todosStore } from './todoStore.js';

function TodosApp() {
  const todos = useSyncExternalStore(
    todosStore.subscribe,
    todosStore.getSnapshot,
  );
  return (
    <>
      <button onClick={() => todosStore.addTodo()}>Add todo</button>
      <hr />
      <ul>
        {todos.map((todo) => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}

// todoStore.js -> 서드 파티 store의 예시

let nextId = 0;
let todos = [{ id: nextId++, text: 'Todo #1' }];
let listeners = [];

export const todosStore = {
  addTodo() {
    todos = [...todos, { id: nextId++, text: 'Todo #' + nextId }];
    emitChange();
  },
  subscribe(listener) {
    listeners = [...listeners, listener];
    return () => {
      listeners = listeners.filter((l) => l !== listener);
    };
  },
  getSnapshot() {
    return todos;
  },
};

function emitChange() {
  for (let listener of listeners) {
    listener();
  }
}
```

위 코드에서 todosStore 객체는 JS로 구현된 외부 저장소이다.

`useSyncExternalStore` 훅의 인자로 구독과 todos 데이터를 넘겨주는 getSnapshot 메서드를 넣는다.

외부 저장소의 addTodo 메서드를 통해 todo를 추가하고, 변경 사항이 있을 때 리렌더링한다.

```ts
import { useSyncExternalStore } from 'react';

function subscribe(callback: () => void) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);

  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

function getSnapshot() {
  return navigator.onLine;
}

export default function OnlineStatus() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);

  return <p>{isOnline ? '온라인 상태입니다.' : '오프라인 상태입니다.'}</p>;
}
```

다른 예시를 보자. 위 예시는 React가 아닌 브라우저의 값인 `navigator.onLine`를 읽는다.

브라우저가 온라인/오프라인 상태로 바뀌면 `callback`이 실행되고, React는 다시 `getSnapshot()`을 호출해서 최신 값을 읽는다. 이 때 값이 바뀌었으면 컴포넌트를 리렌더링한다.

이 훅은 `Internal Store`가 아닌 `External Store`의 데이터를 구독을 통해 렌더링시켜주는 역할을 수행한다.

### 결론

`useSyncExternalStore` 훅은 외부 '저장소'의 데이터를 바라볼 수 있게 연결해주는 훅이다.
