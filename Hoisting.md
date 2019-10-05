# Hoisting

자바스크립트 엔진은 선언과 할당을 구분하여 모든 선언문을 할당 이전에 먼저 처리합니다.  
이로 인해 변수의 할당 이전에 변수에 접근할 경우 의도하지 않은 값을 얻게되는데 이러한 현상을 호이스팅이라고 합니다.

## var와 function

var선언과 function선언문은 함수 스코프를 가지며, 선언과 동시에 undefined로 초기화됩니다.  

```javascript
function foo() {
  console.log(bar);
  {
    function bar() {}
  }
}
foo(); //undefined 출력
```



## let과 const

let과 const선언문은 블록 스코프를 가지며, 선언 시에 초기화되지 않습니다.  
초기화되지 않기 때문에 할당 이전에 변수에 접근하면 reference error를 발생합니다.  
이처럼 할당 이전에 접근이 불가능한 현상을 '변수가 TDZ에 있다'라고 표현합니다.  

```javascript
function foo() {
  console.log(bar);
  const bar = function() {}
}
foo();
//Reference Error : Cannot access 'bar' before initialization
//bar가 선언되어 스코프 내에 있지만 초기화 이전에 접근하여 에러를 발생

function baz() {
  console.log(bar);
  {
    const bar = function() {}
  }
}
baz();
//Reference Error : bar is not defined
//bar가 현재 스코프 내에 선언되어 있지 않음
```

