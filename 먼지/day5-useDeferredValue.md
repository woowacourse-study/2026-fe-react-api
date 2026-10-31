## useDeferredValue
- 일부 ui 업데이트를 지연시킬수 있는 hook

```
  // App.js
  function App(){
    const [query, setQuery] = useState('');
    const differedQuery = useDeferredValue(query);

    return <SearchResults query={differedQuery} />;
  }

  // SearchResults.js
  function SearchResults({ query }){
    if(query == '') return null;
    
    const albums = use(fetchData(query));

    if(!albums) return null;

    return <>
      {albums.map(album => <span>{album}</span>)}
    </>
  }
```

- 나오게 된 이유
  사용자기 타이핑을 빠르게 하고 렌더링(A)에 바로 반영이 되어야 한다 
  query 값을 이용해서 api 호출을 해서 얻는 값으로 렌더링을 하는 부분은 
  api 호출 응답이 상대적으로 늦게 때문에 렌더링(B)은 시간이 걸린다
  
  - B 렌더링을 하느라 A렌더링이 바로 반영이 안되는 상황에 대한 대책으로 나오게 됨 - 느린 렌더링 
  - 최신 query로 B 렌더링을 하는 과정에서 suspense를 하면서
    화면 전체가 fallback 으로 보여 사용자 경험을 떨어트리는 문제를 해결하기 위해 나옴 - suspense
  
- 반드시 비동기랑 관련되어 있는건 아니고 
  결국 이 hook도 무거운 렌더링과 관련되어 있다고 함

- useDeferredValue 는 값을 늦게 따라오게 만들어 
  그 값을 사용하는 렌더링을 낮은 우선순위로 처리하는 hook

- query = '' -> query = 'a'로 변경
  SearchResults는 a를 받아서 fetchData 를 호출하고 use(fetchData(...))에서 suspend됨
  
  이때 query = 'ab'로 변경 -> input 에 ab로 렌더링됨 
  deferredValue는 a로 유지 함
  SearchResult가 받은 a로 fetchData(...) 응답을 받으면 리액트가 SearchResults 를 rendering함
  이후 deferredValue를 ab로 변경한 렌더링을 백그라운드에서 시도함
  이때 ab 을 받은 SearchResult 가 suspend 되면 'a' 결과를 유지함
  'ab' 에 의한 fetchData가 응답을 받으면 'ab' 인 렌더링이 커밋됨

### reference
- https://ko.react.dev/reference/react/useDeferredValue
- https://chatgpt.com/share/6a47ca9f-2c50-83ee-910b-905e8c2cd30a
- https://codesandbox.io/p/sandbox/svfdxs?file=%2Fsrc%2FApp.js
