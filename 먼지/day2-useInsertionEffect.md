## useImperativeHandle

- CSS-in-JS 라이브러리를 만드는 개발자 사용자를 위한 것
  ```
    function Button(){
      return <button css={{ color: 'red' }}></button>
    }
  ```
  1. JS 코드로 해석하기
  2. css 문자열 만들기
  3. style 태그 생성
  4. document.head 에 삽입

  이런 과정을 거치는데
  style을 너무 늦게 넣으면
  a. 컴포넌트 렌더링 -> b. 레이아웃 계산 -> c. <style> 삽질
  b 타이밍에 브라우저가 width, height, position 을 계산해버려 
  레이아웃 계산 -> 스타일 변경 -> 다시 레이아웃 계산 
  이 발생하여 속도가 느려지는 문제가 발생한다

  공식문서에에서 <style> 를 주입하는 방법을 권장하지 않는다 라는게 이 이야기 인듯

  style 태그 주입시 발생하는 문제 중 2번째 문제를 해결할 수 있도록 나온 hook 같음 
  2. 런타임 주입이 React 생명주기 중에 잘못된 시점에 발생하면 속도가 매우 느려질 수 있습니다.

- 실행되는 순서
  render -> useInsertionEffect -> DOM Commit -> useLayoutEffect -> 브라우저 Painting -> useEffect


- 헷갈리는것 
  - 공식문서 내용 중 `interleaving`: AAABBB 가 아닌 ABABAB 번갈아 갈아끼우면 실행됨

### reference
- https://ko.react.dev/reference/react/useInsertionEffect#usage
- https://chatgpt.com/share/6a43c1c6-8d60-83ee-92c8-f7b005148dd3