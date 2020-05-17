## Callback

### 단점

- 비동기 작업을 순차적으로 해야하는 경우 콜백헬이 발생하기 때문에 다음과 같은 단점을 갖는다.

1.  코드의 가독성이 떨어진다.
2.  에러처리가 어렵다. 중첩된 콜백함수마다 try catch로 감싸줘야하기 때문.

```js
try {
  setTimeout(() => { throw ‘Error!’ }, 1000);
} catch (err) {
  console.error(err);
}
//setTimeout에 전달된 콜백함수 내부에서도 try catch로 에러처리를 해줘야 한다. 콜백함수가 또 다른 비동기 함수인 경우 해당 함수의 콜백 내부에서도 또 에러처리가 필요하다. 이런식으로 콜백 패턴은 에러처리가 번거롭다.
//작은 함수로 잘 나눈다고 해도 아래와 같은 형태가 된다. 출처 mdn.
chooseToppings(function(toppings) {
  placeOrder(toppings, function(order) {
    collectOrder(order, function(pizza) {
      eatPizza(pizza);
    }, failureCallback);
  }, failureCallback);
}, failureCallback);
```

---

## Promise

### 장점

프로미스는 콜백 패턴의 단점을 보완하기 위해 도입된 패턴으로 크게 3가지 장점을 갖습니다.

1. 콜백헬 대신 메서드 체이닝을 활용함으로써 코드 가독성이 좋아집니다.
2. 여러 작업의 에러처리를 catch 메서드로 한번에 할 수 있습니다.
3. 여러 작업의 병렬처리를 all 메서드로 쉽게 할 수 있습니다.

### 단점

단점이라고 하기엔 조금 애매하지만, 기존의 동기 코드에 비해 조금 복잡해 보인다는 단점이 존재합니다.

---

## Async/await

### 장점

- 비동기 코드를 동기 코드 처럼 보이게 해서 직관적이다.
- async 함수는 프로미스를 리턴하기 때문에 여전히 프로미스 패턴과 함께 사용할 수 있다.
- try catch로 에러처리가 가능하다.
- 물론 catch메서드를 활용한 에러처리도 가능하다.

### 기타

- async/await을 바벨을 이용해서 es6 코드로 변환하면 generator와 yield 키워드를 활용한 코드로 변환된다.
- es5 코드로 변환하면 regeneratorRuntime이라는 바벨의 generator 구현 모듈과 switch case를 활용한 코드로 변환된다. regeneratorRuntime 모듈은 promise 패턴을 활용해 구현한 듯.
