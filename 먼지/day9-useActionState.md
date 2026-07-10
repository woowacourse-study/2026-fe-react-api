## useActionState
- Action을 사용해 사이드 이펙트를 동반한 상태 업데이트를 할 수 있게 해주는 hook

```js
  async function reducerAction(prev, actionPayload){
    switch(actionPayload.type){
      case 'XXX':
        return await fetch()
    }

    return prev;
  }

  const [state, dispatchAction, isPending] = useActionState(reducerAction, initialState, permalink?);
  

  function handleClick(){
    dispatchAction({ type: 'XXX' });
  }
```

- 해결하고자 하는 문제 
  - 사용자의 action 자체가 비동기 인데 왜 이것을 상태와 분리해서 사용하나?

- read 보다 write를 염두에 두고 나온 hook
  - get 과 같은 조회 보다는 action(post, put, patch, delete) 를 염두에 두고 나온 hook - write
  - query -> suspense/use()
  - mutation -> useActionState

- dispatchAction 은 startTransition 같은 action 안에서 실행햐야 함


- <form action={} />
```
  function login(prev, formData){
    
  }
  const [state, formAction, isPending] = useActionState(login, initialState);

  <form action={formAction} />
```

### reference
- https://ko.react.dev/reference/react/useActionState
- https://chatgpt.com/share/6a4fc66d-08fc-83ee-a7d4-c10180b56229
