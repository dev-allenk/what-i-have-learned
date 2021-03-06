# Closure와 Scope

## Scope

스코프란, 변수의 식별자와 값을 기록해둔 데이터를 의미합니다.
스코프는 전역 코드나 함수 코드가 실행이 될 때 생성됩니다.
스코프는 생성될 때, 선언된 환경 내에서 자신의 상위 스코프를 참조합니다.
따라서 해당 스코프 내에서 사용된 변수는 상위 스코프에 접근해서 값을 읽을 수 있게 되는데, 이를 스코프 체인이라고 합니다.

## Closure

클로저란, 어떤 함수가, 자신이 선언된 스코프를 기억해서, 해당 스코프 밖에서 호출되더라도, 해당 스코프의 변수에 접근해서 값을 읽을 수 있는 현상, 또는 이러한 함수를 말합니다.

---

### 클로저 예시 코드를 작성해보세요

```javascript
function makeCounter() {
  let count = 0;
  return function () {
    console.log(++count);
  };
}
const counter = makeCounter();
counter();
```

---

### 클로저 관련 문제

다음 코드의 실행 결과를 예상하고, 의도에 맞게 표현하려면 어떻게 수정해야 하는 지 코드를 작성해보세요.

```javascript
for (var i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 1000);
}
```

#### 답

실행 결과 : 5가 5번 출력됩니다.

수정 코드 : 즉시 실행함수와 변수가 하나 더 필요합니다.

```javascript
//1. setTimeout의 콜백함수를 즉시 실행함수로 감싸기
for (var i = 0; i < 5; i++) {
  setTimeout(((x) => () => console.log(x))(i), 1000);
}
or;
//2. setTimeout바깥을 즉시 실행함수로 감싸기
for (var i = 0; i < 5; i++) {
  ((x) => setTimeout(() => console.log(x), 1000))(i);
}
//1번 케이스 조금 쉽게
for (var i = 0; i < 5; i++) {
  setTimeout(
    (function (x) {
      return function () {
        console.log(x);
      };
    })(i),
    1000
  );
}
```

참고로, 수정 코드의 실행 결과는 0부터 4까지 출력됩니다.

---

실행 가능한 코드를 만나면 스코프를 생성한다.  
스코프는 코드를 실행하기 위해 식별자와 그 값을 기록한 자료구조이다.

실행 가능한 코드는 3가지가 있다.

1.  전역 코드
2.  함수 코드
3.  eval 코드

스코프의 생성은 크게 2단계로 이루어진다.

1.  creation phase
2.  execution phase

createion phase에는 3가지 작업이 이루어진다. 선언문을 처리하다고 생각하면 된다.

1.  (함수 코드의 경우) 매개변수의 식별자와 값을 스코프에 기록
2.  function 선언문은 식별자에 function name을, 값에는 function 객체를 기록 (함수객체 생성시 [[Environment]]속성(=[[scope]])이 global LexEnv를 참조)
3.  var 선언은 값을 undefined로 초기화
4.  let, const 선언인 경우 식별자만 기록하고 값은 초기화하지 않음

execution phase에는 표현식을 값으로 평가한다. 할당, 연산, 함수호출, 리턴이 해당된다. 하나의 값으로 평가가 끝나면 해당 값을 스코프에 기록한다.
