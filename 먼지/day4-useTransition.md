## useTransition
```
  const [isPending, startTransition] = useTransition();
```
- 동시성으로 React 구현이 변경되면서 렌더링 작업에 우선순위를 둘 수 있게 됨
- React 는 state 업데이트로 인한 렌더링을 중요도 등에 따라 우선순위를 관리한다
- 이 hook은 특정 업데이트를 급하지 않는 작업으로 마크해서 React가 낮은 우선순위로 처리한다

- 어떤 문제를 해결하기 위해 나왔나?
  - 렌더링이 오래 걸리는 작업을 하느라 사용자 입력 등 중요한 렌더링이 늦어지는 문제
  - API 호출시의 비동기에 관한 게 아니다

- 문제 설명하기 위한 실제 예시 
  - A: 큰 리스트(10000개 리스트 렌더링) -> useTransition 사용 필요 
  - B: 검색시 검색어 타이핑 -> 바로 반영 필요 

- before
  ```
    // before
    const [keyword, setKeyword] = useState('')
    const [filteredProducts, setFilteredProducts] = useState([...])

    return (<>
      <input />
      <ProductList products={filteredProducts} />
    </>)/
  ```
- after
  ```
    // after
    const [keyword, setKeyword] = useState('')
    const [filteredProducts, setFilteredProducts] = useState([...])

    const [isPending, startTransition] = useTransition();

    const updateFilteredProducts = (newInput) => {
      setKeyword(newInput);
      startTransition(() => {
        setFilteredProducts\([...])
      });
    }

    return (<>
      <input value={keyword} onChange={(e) => updateFilteredProducts(e.target.value)} />
      <ProductList products={filteredProducts} />
    </>);
  ```

### reference
- https://ko.react.dev/reference/react/useTransition
- https://chatgpt.com/share/6a467a22-3484-83e8-9d10-226380001a42