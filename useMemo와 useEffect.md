# useMemo vs. useEffect

언뜻 보면 비슷해보이는 useMemo와 useEffect!  
둘 다 어떤 '값'이 변경될 때 실행된다는 공통점이 있지요.  
실행되는 동작은 비슷하지만 분명한 차이점이 존재합니다.  
예제코드를 작성해보면서 차이점을 알아볼게요.  



## 공통점

먼저 useMemo는 아래와 같이 사용되지요.

```javascript
const data = useMemo(() => foo(value), [value])

const foo = v => v + 1;
```

value변수가 변경이 될 때, useMemo의 콜백함수를 실행하고 리턴값을 data변수로 받아서 사용할 수 있어요.

그런데, 아래처럼 useState와 useEffect를 사용해도 같은 일을 할 수 있어요.

```javascript
const [data, setData] = useState(0);

useEffect(() => {
	setData(value + 1);
}, [value]);
```

위 코드도 value변수가 바뀌면 useEffect의 콜백함수가 실행되어 data값을 바꿔주지요.

useMemo, useEffect 둘 다 일종의 조건부 함수로 **어떤 값이 변경될 때만 실행되게 하고 싶을 때** 사용할 수 있어요.

이렇듯 같은 일을 하는 것처럼 보이는데 차이점은 무엇일까요?



## 차이점

한가지 차이점은 useMemo와 useEffect가 같은 일을 하도록 작성한 경우,  
**컴포넌트의 렌더링 수가 다르다**는 점이에요.

리액트 공식홈페이지의 useEffect 설명 중에는 이런 문구가 있어요.

> The function passed to `useEffect` will **run after the render** is committed to the screen.

useEffect는 렌더링 이후에 실행할 effect함수를 예약하는 hooks라고 할 수 있어요.  
useEffect는 렌더링이 한번 발생한 이후에 effect함수를 실행하기 때문에,  
**만약 effect함수가 렌더링을 발생시킨다면, 결과적으로 렌더링이 2번 발생해요!**

반면, useMemo 설명 중에는 이런 문구가 있어요.

> Remember that the function passed to `useMemo` **runs during rendering.**

useMemo의 콜백함수는 **렌더링 도중에 실행**된다!

즉, useMemo와 같은 일을 하기위해 useEffect를 사용하는 경우에는 useState를 함께 사용해야 하는데,  
effect함수는 렌더링 이후에 실행되기 때문에 추가적인 렌더링이 발생한다고 할 수 있어요.  
(물론, useMemo의 콜백함수에서 setState를 호출하는 경우에는 추가적인 렌더링이 발생하겠지만,  
그렇게 작성할 필요가 있는지 모르겠네요.)

자 그럼, 예제 코드를 보면서 확인해볼게요.

[![Edit useMemo vs. useEffect 예제](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/usememo-vs-useeffect-yeje-n4j8s?fontsize=14) 



### useMemo 예제코드

먼저, useMemo를 사용한 코드에요.

버튼을 클릭하면, num값이 바뀌고, useMemo로 인해 result값이 변경되어 렌더링돼요.

이 때, 전역에 있는 count변수는 App함수가 호출될 때마다 증가하여 컴포넌트의 렌더링 수를 확인할 수 있어요.

```javascript
let count = 0;

function App() {
  const [num, setNum] = useState(0);
  const result = useCalculateWithMemo(num);

  const handleClick = () => {
    console.log("clicked");
    setNum(num + 1);
  };

  return (
    <>
      <div>App함수 호출 횟수 : {++count}</div>
      <button onClick={handleClick}>클릭</button>
      <h1>calc result : {result}</h1>
    </>
  );
}

const useCalculateWithMemo = numberProp => {
  console.log("withMemo called");
  return useMemo(() => numberProp + 1, [numberProp]);
};
```

#### useMemo 실행화면

![withMemo](https://raw.githubusercontent.com/revlanc/etc/master/useMemo_useEffect/withMemo.gif?token=AKHK67DJ2NWJAGMN6K7NN7C5QYJ7I)

코드를 실행해보면 위와 같은 모습을 볼 수 있는데요,  
num값이 변경될 때마다 App컴포넌트도 1번씩 렌더링되는 것을 볼 수 있어요.  
num값이 변경되어 App컴포넌트가 렌더링되는 도중에 useMemo의 콜백이 실행되기 때문에,  
result값의 변경이 바로 반영되는 것이지요.



### useEffect 예제코드

아래는 같은 동작을 useEffect로 작성한 코드에요.  
과정에 약간 차이가 있어요!

버튼을 클릭해서 num값이 바뀌면, **effect함수의 실행을 예약하고** 일단 렌더링이 1회 발생해요.  
그 다음에 effect함수가 실행되어 result값이 바뀌고, 렌더링이 다시 발생해요.

```javascript
let count = 0;

function App() {
  const [num, setNum] = useState(0);
  const result = useCalculate(num);

  const handleClick = () => {
    console.log("clicked");
    setNum(num + 1);
  };
  
  return (
    <>
      <div>App함수 호출 횟수 : {++count}</div>
      <button onClick={handleClick}>클릭</button>
      <h1>{result}</h1>
    </>
  );
}

const useCalculate = numberProp => {
  const [result, setResult] = useState(null);
  console.log("withoutMemo called");

  useEffect(() => {
    setResult(numberProp + 1);
  }, [numberProp]);

  return result;
};
```

#### useEffect 실행화면

![withoutMemo](https://raw.githubusercontent.com/revlanc/etc/master/useMemo_useEffect/withoutMemo.gif?token=AKHK67DIOEGKP6C7U2TBL325QYKBY)

코드를 실행해보면 위와 같은 모습이에요.  
num값이 변경될 때마다 App컴포넌트가 2번씩 렌더링되는걸 확인할 수 있어요.

이처럼, effect함수에서 state의 변경이 일어난다면 불필요한 렌더링이 발생할 수 있기 때문에,  
퍼포먼스를 고려해야한다면 적절한 hooks를 사용해야겠네요.



## useMemo 활용하기

이제까지 알아본 useMemo의 특징은 다음과 같아요.

1. 어떤 값이 변경될 때만 실행되게 하고싶을 때 사용한다.
2. 실행의 결과가 렌더링에 반영이 되는 경우, useEffect에 비해 렌더링 수가 적다.

여기서 1번 특징은 React.memo() 메서드와 함께 사용되어 하위 컴포넌트의 렌더링을 최적화할 때 유용하게 사용돼요.

예제코드를 보면서 확인해볼게요.

### useMemo 활용 예제

조금 복잡해보일 수 있는데 천천히 살펴볼게요.

우선, 버튼을 클릭하면 value변수가 바뀌면서 App컴포넌트의 렌더링이 발생하고,  
하위 컴포넌트인 DisplayOne과 DisplayTwo가 함께 렌더링 되는 구조에요.

count함수가 todoData를 받아서 todoCount와 doneCount를 객체에 담아 리턴하고,  
하위 컴포넌트에서 이 객체를 props로 받아서 렌더링을 해요.

여기서 todoData는 value변수와는 상관없는 상태이기 때문에,  
하위 컴포넌트의 렌더링을 방지하기 위해 React.memo를 사용했어요.

```javascript
const todoList = [{ status: "todo" }, { status: "todo" }, { status: "done" }];

const count = data => {
  const todoCount = data.filter(v => v.status === "todo").length;
  const doneCount = data.filter(v => v.status === "done").length;
  return { todoCount, doneCount };
};

function App() {
  const [todoData, setTodoData] = useState(todoList);
  const [value, setValue] = useState(true);

  const countWithMemo = useMemo(() => count(todoData), [todoData]);
  const countsWithoutMemo = count(todoData);

  const handleClick = () => {
    setValue(!value);
  };

  return (
    <div className="App">
      <button onClick={handleClick}>click!</button>
      <DisplayOne counts={countWithMemo} />
      <DisplayTwo counts={countsWithoutMemo} />
      {value && <h1>test</h1>}
    </div>
  );
}

const DisplayOne = React.memo(props => {
  console.log("with memo rendered");
  return <h1>with memo : {props.counts.todoCount}</h1>;
});

const DisplayTwo = React.memo(props => {
  console.log("without memo rendered");
  return <h1>without memo : {props.counts.doneCount}</h1>;
});

```

[![Edit useMemo예제](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/usememoyeje-d0z8l?fontsize=14)

React.memo는 props를 이전 props와 얕은 비교를 해서 변화가 없다면 컴포넌트를 렌더링하지 않아요.  
그런데, count함수에서 리턴하는 값은 객체이기 때문에,  
App컴포넌트의 렌더링이 발생하여 count함수가 호출될 때마다 새로운 객체가 만들어져요.  
새로운 객체라는 말은 이 객체가 props.counts로 전달되었을 때, 이전 props.counts와 다른 값이라는 뜻이고,  
이는 렌더링을 발생한다는 의미이지요. (예제코드에서 DisplayTwo의 경우)  
todoCount와 doneCount값은 변하지 않았는데도 말이지요.  

이 현상을 해결하는 방법은 여러가지가 있는데 여기서는 useMemo를 이용해 보았어요.  
countWithMemo는 todoData가 변경될 때만 새로운 값으로 바뀌어요.  
그래서 클릭이 발생하더라도 DisplayOne은 렌더링을 방지할 수 있어요.   

참고로, props를 object타입이 아닌 값으로 넘겨주거나, React.memo의 두번째 인자로 비교함수를 작성해서 렌더링을 제어할 수도 있어요.  



#### useEffect 실행화면

![useMemo예제](https://raw.githubusercontent.com/revlanc/etc/master/useMemo_useEffect/useMemo%EC%98%88%EC%A0%9C.gif?token=AKHK67EDBIPASPNC7UCQ3BS5QYKDW)

DisplayOne컴포넌트는 불필요한 렌더링이 되지 않네요!



## 정리

리액트 공식문서를 보면 useMemo 항목의 첫 줄에 이렇게 적혀있어요.

> Returns a memoized value.

useMemo는 메모이제이션된 값을 리턴하는 함수라는 뜻인데요,  
동작을 조금 생각해보면 이렇게 정리할 수 있어요.

> 두번째 인자로 받은 의존성 배열요소의 '값'을 비교해서, 기존 값과 같으면 콜백함수를 실행하지 않고,  
> 메모이제이션된 값을 리턴해주는 함수.

공식문서에도 나와있듯이 콜백함수가 계산비용이 많이 드는 함수라면 useMemo로 성능을 향상시킬 수 있겠지요?

앞으로 hooks를 이용해 코드를 작성할 때 useMemo와 useEffect의 특징을 생각하면서 작성해보아요.:smile:



### 요약

***useEffect는 렌더링 이후에 실행할 effect함수를 예약하는 hooks이다.***

***useEffect에서 state를 변경하는 경우 추가적인 렌더링이 발생할 수 있다.***

***useMemo는 의존하는 값이 변경되지 않으면, 메모이제이션된 값을 리턴하는 hooks이다.***

***useMemo를 적절히 활용하면 렌더링 성능을 최적화할 수 있다.***





## reference

- https://stackoverflow.com/questions/56028913/usememo-vs-useeffect-usestate
- https://reactjs.org/docs/hooks-reference.html#usememo

- https://reactjs.org/docs/react-api.html#reactmemo
- https://ui.toast.com/weekly-pick/ko_20190731/
