리액트에는 상태 관리를 위해 useState와 useReducer라는 2가지 hook을 제공하고 있습니다.  
useReducer는 사실 useState의 다른 모습인데요, 이번 글에서는 useReducer와 같은 동작을 하는 커스텀훅을 만들어보면서 useReducer를 이해해보고자 합니다. (useReducer의 기본적인 사용법은 다루지 않아요! 사용법은 공식문서를 참고해주세요.)

## useState 살펴보기

먼저 useState에 대해 간단히 살펴볼게요.
useState는 사용하는 패턴이 2가지가 있어요.

```jsx
//1. updater function에 새로운 상태를 전달하는 패턴
const increase = () => setState(state + 1);

//2. updater function에 callback 함수를 전달하는 패턴
const increase = () => setState((state) => state + 1);
```

위 두가지 패턴에서 `state + 1` 부분은 newState에요. 새로운 상태이지요.  
2번 패턴의 경우에는 setState에 함수가 전달되면 그 함수를 실행하고, 함수가 리턴한 값이 newState가 된다고 이해하면 됩니다.  
여기서 우리가 기억해야 할 것은 *setState에 새로운 상태를 전달하면 컴포넌트의 상태가 업데이트 된다*는 점이에요.

> setState에 새로운 상태를 전달하면 컴포넌트의 상태가 업데이트 된다.

## useReducer 살펴보기

다음으로 useReducer를 간단히 살펴볼게요.
useReducer는 아래와 같이 사용해요.

```jsx
const [state, dispatch] = useReducer(reducer, 0)

const increase = () =>
  dispatch({ type: ‘increment’, payload: 1 })

function reducer(state, action) {
  if(action.type === ‘increment’) {
    return state + payload;
  }
 return state;
}
```

useReducer는 dispatch함수에 어떤 값을 전달하고, 여기서 전달된 값은 reducer 함수의 2번째 인자로 전달되는 구조에요. 그리고 reducer는 새로운 상태를 리턴하지요.

> reducer가 새로운 상태를 리턴하면 컴포넌트의 상태가 업데이트 된다.

## dispatch의 이해

이 시점에서 useState를 사용하는 패턴을 다시 볼까요?

```jsx
setState(state + 1);
//or
setState((state) => state + 1);
```

reducer가 리턴한 `state + payload` 와 setState에 전달된 값이 비슷해보이네요.
둘 다 ‘새로운 상태’이니 비슷할 수 밖에요.

> setState에 전달되는 값과 reducer 함수가 리턴하는 값은 둘 다 똑같은 '새로운 상태'이다.

그렇다면 reducer가 리턴한 값을 setState에 전달하면 같은 동작이 일어나겠죠?  
대충 코드로 적어보면 이런 모양이 되겠네요.

```jsx
const increase = () =>
  setState(reducer(state, { type: ‘increment’, payload: 1 })
```

콜백함수를 사용한 패턴으로도 작성할 수 있을거에요.

```jsx
const increase = () =>
  setState(state =>
    reducer(state, { type: ‘increment’, payload: 1 })
```

뭔가 dispatch 함수와 비슷한 느낌이 나지 않나요?  
dispatch함수는 action을 인자로 받는 함수였죠.  
action부분을 인자로 받도록 바꾸면서 이름도 조금 바꿔볼게요.

```jsx
const dispatch = action =>
  setState(state => reducer(state, action))

const increase = () =>
  dispatch({ type: ‘increment’, payload: 1 })
```

맞아요. 사실 dispatch 함수는 약간 다르게 생긴 setState 함수에요.  
reducer함수와의 조합으로 만들어진 함수이지요.

> dispatch 함수는 setState와 reducer의 조합이다.

## useReducer의 이해

dispatch에 대해 이해했으니 이번에는 useReducer를 이해해볼 차례에요.

우선, useReducer에 대해 알고 있는 사실을 몇가지 적어볼게요.

1. useReducer는 함수이다.
2. reducer함수, initialState, init함수를 인자로 전달받는다.
3. state와 dispatch함수를 리턴한다.
4. dispatch함수는 사실 setState함수이다.

useReducer는 결국 함수이고, 이름이 use로 시작하는 걸로 봐서 *상태를 포함한 로직을 담고 있는 함수*인 것을 알 수 있어요.
*상태를 포함한 로직*이란, 이 함수 내부에서 useState와 같은 hook을 사용한다는 의미에요.

useState와의 관계에 조금 더 초점을 맞추어 보면 이런 점을 알 수 있어요.

1. useState hook은 setState를 리턴한다.
2. useReducer 함수 내부에서 useState hook을 호출할 수 있다.
3. useReducer가 리턴하는 state와 dispatch는 useReducer함수 내부에 정의되어 있다.
4. dispatch함수는 setState와 reducer의 조합으로 만들 수 있다.

이쯤되면 왠지 useState를 이용해서 useReducer를 만들 수 있을 것 같지 않나요?  
바로 한번 만들어보죠!(3번째 인자인 init함수는 제외할게요.)

### useReducer 작성하기

먼저 함수의 전체적인 모양을 잡아볼게요.  
reducer와 initialState를 인자로 받고 state 와 dispatch 함수를 리턴하는 모양이죠.

```jsx
const useReducer = (reducer, initialState) => {
  return [state, dispatch];
};
```

dispatch함수는 setState로 만들어야하니 useReducer 내부에서 useState를 호출할거에요.  
useReducer가 2번째 인자로 받은 initialState는 useState에 전달하면 되겠죠.

```jsx
const useReducer = (reducer, initialState) => {
  const [state, setState] = useState(initialState);

  return [state, dispatch];
};
```

여기에 아까 만든 dispatch함수를 추가하면,

```jsx
const useReducer = (reducer, initialState) => {
  const [state, setState] = useState(initialState);

  const dispatch = (action) => setState((state) => reducer(state, action));

  return [state, dispatch];
};
```

간단하게 완성!
맞아요. 사실 useReducer는 약간 다르게 생긴 useState랍니다.

## 닫는 글

여기까지 useReducer를 만들어보면서 useReducer가 사실은 useState와 다르지 않다는 걸 알 수 있었어요. 그럼에도 useReducer를 별도로 제공하는 것은 reducer 함수를 통해 상태 업데이트 로직을 컴포넌트로부터 분리해서 코드를 깔끔하게 정리할 수 있기 때문이지요.  
useReducer를 사용할 때 주의할 점은 reducer함수는 비동기로직을 다루지 못한다는 점인데요, 다음 글에서는 비동기로직을 다룰 수 있는 useReducer를 만드는 방법을 소개할게요.👋
