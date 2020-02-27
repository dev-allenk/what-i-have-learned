# Hoisting

호이스팅은 스코프의 초기화 시점과, 코드를 실행하는 시점의 차이로 인해 발생하는 현상입니다.  
전역코드나 함수코드가 실행될 때 스코프가 생성이 되는데,  
var나 function으로 선언한 식별자는 스코프를 생성함과 동시에 undefined로 초기화됩니다.  
반면 let, const, class로 선언한 식별자는 스코프 생성 시에는 초기화 되지 않습니다.  
따라서 이러한 식별자에 값을 '할당'하기 전에 그 값을 읽으려고 접근하면,  
var나 function으로 선언한 식별자는 undefined를 읽게 되는데, 이러한 현상을 호이스팅이라고 합니다.    
반면 let, const, class로 선언한 식별자는 접근할 수 없다는 에러를 나타내게 됩니다.


---

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

