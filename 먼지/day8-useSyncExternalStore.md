## useSyncExternalStore
- 외부 데이터 조정소에서 값을 읽는 hook

```js
  function TodoApp(){
    const todos = useSyncExternalStore(todoStore.subscribe, totoStore.getSnapShot);
  }
```
- todoStore.subscribe 
  - todoStore.subscribe 를 useSyncExternalStore 가 받아서 리랜디링할 함수를 매개변수로 넣어 실행한다
  - todoStore.subscribe 는 매개변수로 받은 함수를 listeners 에 추가 한다
  - todoStore 에서 관리하는 데이터가 변경시 listeners를 실행시킨다
- totoStore.getSnapShot - 참조할 값 반환

```
  const todoStore = {
    value = [];
    listeners = new Set();
    subscribe(callback){
      this.listeners.add(callback);

      return (){
        this.listeners.delete(callback);
      }
    }
    getSnapShot(){
      return this.value;
    }
    addTodo(todo){
      this.value = [...this.value, ...todo];

      for(const listener of this.listeners){
        listener();
      }
    }
  }
```

- 이 hook 을 사용하고 있는 라이브러리
  - zustand
  - react redux
  - tanstack query

### reference
- https://ko.react.dev/reference/react/useSyncExternalStore
- https://chatgpt.com/share/6a4e6338-5e40-83ee-a41a-18bd178d3ff7
