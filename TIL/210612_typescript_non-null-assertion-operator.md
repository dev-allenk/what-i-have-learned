# non-null assertion operator (!)  

변수에 null이나 undefined가 들어오지 않을 거라고 단언하는 용도.  
만에 하나 null이 들어오면 runtime에 에러난다.  
사용하지 말자.  
type guard를 대신 사용할 것.  

```
let a: string | number;

a!.toString() //don't

if(a !== null || a !== undefined) {
  a.toString() //do
}
```
