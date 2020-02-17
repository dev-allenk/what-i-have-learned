# this와 화살표함수



## 호출 방식에 따른 this 바인딩

자바스크립트의 this 키워드는 함수 호출 방식에 따라 this에 바인딩되는 객체가 달라집니다.(Dynamic this)  
많이 헷갈리는 개념이지만, 함수 호출 시에 함수 왼쪽에 어떤 것이 있는지 보면 쉽게 구분할 수 있습니다.

### 일반적인 함수 호출

기본적으로 함수를 호출하면 함수 내에서 사용된 this는 전역객체에 바인딩 됩니다.  
전역에서의 함수 호출, 내부함수의 호출, 콜백함수의 호출의 경우 모두 전역 객체에 this가 바인딩 됩니다.  
이 경우에는 함수 호출 시 함수 왼쪽에 아무것도 없기 때문에 전역을 가리킨다고 이해할 수 있습니다.  

### 메서드의 호출

어떠한 함수가 객체의 메서드로서 호출되면, 함수 내에서 사용된 this는 메서드를 호출한 객체에 바인딩 됩니다.  
이 경우는 함수 왼쪽에 객체가 있기 때문에 해당 객체를 가리킨다고 이해할 수 있습니다.

### 생성자 함수의 호출

어떠한 함수가 생성자 함수로서 호출되면, 함수 내에서 사용된 this는 함수가 생성하는 객체에 바인딩 됩니다.  
이 경우는 함수 왼쪽에 new 키워드가 있기 때문에 새로 생성되는 객체를 가리킨다고 이해할 수 있습니다.



## this를 변경하는 방법

이렇듯 호출 방식에 따라 달라지는 this를 원하는 대로 바인딩하기 위한 방법이 2가지가 있습니다.  
call, apply, bind 메서드를 사용하는 것과 화살표함수를 사용하는 방법입니다.

### call, apply, bind

call, apply, bind는 Function.prototype의 메서드로서 this를 의도적으로 바인딩하기위한 메서드입니다.  
call과 apply는 바인딩할 객체와 함수에 전달할 인자를 받아서 함수를 즉시 실행하는 메서드입니다.  
call은 전달할 인자를 하나씩 값으로 받지만, apply는 전달할 인자를 배열로 받는다는 차이점이 있습니다.  
그리고 bind는 this가 바인딩 된 새로운 함수를 리턴합니다.

```javascript
//call메서드의 활용 - 메서드를 동적으로 추가하는 경우에 사용할 수 있다.
var util = function() {
   this.getName = function() {
     return this.name;
   }
   this.setName = function(name) {
     this.name = name;
   }
}

class Car { 
 constructor(price, name) {
    this.price = price;
    this.name = name;
 }
 getPrice() {
   return this.price;
 }
}

util.call(Car.prototype);

var car = new Car(999,"Ford");
car.getPrice(); //999
car.getName();  //Ford

//(this키워드 와는 관계가 없지만)apply활용
const arr2 = [2, 5, 1, 3, 6];
const max = Math.max.apply(null, arr2); // === Math.max(...arr2)
console.log(max); // 6
```



### 화살표함수

ES6에 추가된 문법인 화살표함수는 호출 방식에 따라 바인딩이 달라지는 것이 아니라 this 바인딩이 정적으로 결정됩니다.  
화살표 함수 내에서 사용된 this는 항상 '선언된 당시 상위 스코프의 this'를 참조합니다.  
bind메서드의 syntactic sugar로서 매우 유용하게 사용됩니다.
