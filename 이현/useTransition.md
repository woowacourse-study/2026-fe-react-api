## useTransition

### 요약

React에서 상태를 업데이트할 때 우선순위를 부여할 수 있는 훅
useTransition 자체는 매개 변수없고, 2개의 반환값이 있다.
하나는 isPending -> 대기중인 transition이 있는지 없는지 확인용
startTransition -> 상태를 업데이트하는 동작을 1개이상 포함한 콜백을 하나 받아서 실행하는 함수

UX를 위한 훅이다.

### 주요 용도

검색 결과로 데이터를 받아올 때, 서버로부터 데이터를 받아와 서버 상태를 update할 때 사용된다. 검색 쿼리 기반으로 데이터를 불러와 update하는 작업을 transition을 이용하면, 마지막 검색 쿼리의 데이터에 대해서만 결괏값을 보여줄 가능성이 높다.

비슷하게 tab을 이용해 페이지 이동을 할 때도 사용된다. 페이지를 이동했을 때 필요한 데이터를 받아와서 상태를 update하는 부분을 startTransition의 콜백함수 안에 넣으면 사용자가 탭을 왔다갔다 해도 마지막 탭의 정보만 보여줄 수 있다.

### 예시

```tsx
export default function App({}) {
  const [quantity, setQuantity] = useState(1);
  const [isPending, startTransition] = useTransition();

  const updateQuantityAction = async (newQuantity) => {
    // transition의 보류 중인 상태에 액세스하려면,
    // startTransition을 다시 호출하세요.
    startTransition(async () => {
      const savedQuantity = await updateQuantity(newQuantity);
      startTransition(() => {
        setQuantity(savedQuantity);
      });
    });
  };

  return (
    <div>
      <h1>Checkout</h1>
      <Item action={updateQuantityAction} />
      <hr />
      <Total quantity={quantity} isPending={isPending} />
    </div>
  );
}

수량이 바뀔 때마다 total이 업데이트 되는 부분을 transition으로 감싸서 업데이트 요청이 계속 와도 결국 마지막에 받아온 값만 화면에 나타남.
```

### 질문 모음

Q1. await 이후에 왜 startTransition을 또 하는 것일까?

비동기 요청 이후에 상태 update로직이 존재하는 경우에는 비동기 이후 로직을 startTransition으로 한 번 더 감싸야한다.

React는 await 이후의 상태 업데이트를 transition으로 처리하지 않는다.
아마 await를 만나면 현재 실행중이던 startTransition의 실행 컨텍스트가 콜스택에서 빠지고, 그 이후 작업들이 새로운 Promise의 reaction job으로 등록되기 때문에 transition 맥락을 잃어버리는 듯하다.

Q2. 가장 최신 update를 보여주는 것에 특화되어 있는 훅 같은데 race condition 문제는?

완료 순서는 보장하지 못하기 때문에 race condition 문제는 개발자가 직접 잘 처리해야한다.

Q3. startTransition의 콜백은 동기적으로 처리되는가?

startTransition에 전달된 콜백 함수는 동기처리되어 즉시 실행된다.
전달받은 콜백의 실행 순서를 바꾸는 것이 아니다.
받은 콜백 내에서 실행되는 `상태 update`의 렌더링 순서를 조절하는 것이다.

Q4. 어떻게 마지막 update만 보여줄 수 있는 것인가?

stratTransition내에서 실행되는 상태 update는 특별하게 동작한다.
일단 update된 상태 기반으로 렌더링 스냅샷은 만든다. 하지만 이후에 새로운 update가 오면, 이전 렌더링 스냅샷을 폐기하고 새로운 update를 적용한다.
그리고 최신 update만 DOM에 commit 된다.
