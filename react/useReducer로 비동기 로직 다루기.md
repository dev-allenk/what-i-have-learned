# useReducer로 비동기 로직 다루기

## 여는 글

리액트는 정말 인기가 많은 라이브러리이지요. 높은 자유도가 그 인기에 한몫한다고 생각해요. 하지만 코드를 작성할 때, 높은 자유도로 인해 어떤 형태로 작성하는 것이 좋을지 많은 고민을 하게 되기도 하지요. 저는 특히 비동기 로직을 작성하면서 이런 고민을 많이 했어요.  
이번 글에서는 useReducer를 사용할 때 비동기 로직을 다루는 패턴을 살펴보고, 지난 글에서 만들었던 useReducer를 조금 수정해서 비동기 로직도 다룰 수 있는 useReducer로 만들어 볼게요.

## useReducer의 단점

useReducer는 컴포넌트로부터 로직을 분리할 수 있게 해주는 유용한 hook이에요.  
하지만 비동기 로직과 함께 사용하는 방법은 제공하지 않는다는 단점이 있어요.  
왜냐하면 reducer 함수는 순수 함수로 작성해야 하기 때문에, async 함수가 될 수 없기 때문이지요.

```javascript
//이런 코드를 작성할 수 없어요.
async function reducer(state, action) {
  switch(action.type) {
    case 'fetchData':
      const res = await fetch('...');
      const data = await res.json();
      return { ...state, data };
    //...
}
```

마찬가지로, async 함수가 될 수 없기 때문에 Promise 타입의 action도 받지 못하는 단점이 있어요.

```javascript
//이런 코드를 작성할 수 없어요.
async function reducer(state, action) {
  switch(action.type) {
    case 'promiseType':
      const data = await action;
      return { ...state, data };
    //...
}
```

## 리액트에서 비동기 로직을 다루는 패턴

따라서 useReducer는 기본적으로 '비동기 로직'을 포함하는 '비동기 액션'을 dispatch 하지 않아요. 대신, 비동기 함수를 dispatch 이전에 호출해서 비동기 로직을 실행하고, 그 결과 값을 dispatch 하지요.  
이런 방식으로 비동기 로직을 작성하는 방법은 몇가지 패턴이 있을 수 있는데요, 지금부터는 제가 생각하기에 가독성 좋고 깔끔하다고 생각되는 패턴을 몇가지 소개해드릴게요.  
참고로 아래 패턴들은 비동기 함수를 '잘 추상화된 1개의 함수'로 만들어서 호출하는 것을 전제로 합니다. 왜냐하면 작게 나누어진 여러개의 함수를 리액트 컴포넌트 내부에서 호출하면 컴포넌트가 복잡해보이더라구요 :)

### 1. 비동기 작성 패턴 - 첫번째

**핸들러 함수에서 에러처리 하는 패턴**

```jsx
function App() {
  const [state, dispatch] = useReducer(reducer, {});

  const onClick = async () => {
    dispatch({ type: "fetchStart" });
    const { isError, data, error } = await getUser(id);

    isError
      ? dispatch({ type: "fetchError", payload: error })
      : dispatch({ type: "updateUser", payload: data });

    dispatch({ type: "fetchEnd" });
  };

  return (
    <>
      <h1>{state.userId}</h1>
      <h2>{state.loading ? "fetchStart" : "fetchEnd"}</h2>
      <button onClick={onClick}>fetch</button>
    </>
  );
}

async function getUser(id) {
  try {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/users/${id}`
    );
    const data = await response.json();
    return { isError: false, data };
  } catch (error) {
    console.error(error);
    return { isError: true, error };
  }
}
```

- 비동기 함수에서 에러처리는 필수라고 할 수 있죠. 이 패턴은 비동기 작업 도중 발생하는 에러처리의 책임을 리액트 컴포넌트에 부여한 패턴이에요.
- onClick이라는 핸들러 함수 내부에서 에러처리를 포함한 로직의 흐름이 잘 드러나는 장점이 있어요.
- "`fetchStart -> getUser -> updateUser or fetchError -> fetchEnd`" 라는 흐름을 한눈에 파악할 수 있지요?
- 이 패턴도 충분히 깔끔하지만 비동기 작업 과정에서 발생한 에러를 이벤트 핸들러에서 처리한다는게 어색할 수도 있어요. 그런 경우에는 다음에 소개할 패턴을 활용할 수 있어요.

### 2. 비동기 작성 패턴 - 두번째

**비동기 함수 내부에서 에러처리 하는 패턴**

```jsx
//...
const onClick = async () => {
  dispatch({ type: "fetchStart" });
  dispatch(await getUser(id));
  dispatch({ type: "fetchEnd" });
};
//...

async function getUser(id) {
  try {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/users/${id}`
    );
    const data = await response.json();
    return { type: "updateUser", payload: data };
  } catch (error) {
    console.error(error);
    return { type: "fetchError", payload: error };
  }
}
```

- 비동기 함수가 액션 객체를 리턴하도록 작성하는 패턴이에요.
- 이 패턴은 핸들러 함수를 단순하게 작성할 수 있다는 장점이 있어요.
- 비동기 과정에서 발생하는 에러가 어떻게 처리되는지가 핸들러 함수에서 확인되지는 않지만 리액트 컴포넌트 입장에서는 이벤트가 발생했다는 사실만 전달하면 그 역할을 다 한거라고 볼 수도 있지요.
- 이 패턴의 경우에는 dispatch에서 액션 객체를 리턴하는 함수를 호출해서 사용하면 일관성있는 형태로 코드를 작성할 수도 있어요. 아래처럼요.

```jsx
//...
const onClick = async () => {
  dispatch(fetchStart());
  dispatch(await getUser(id));
  dispatch(fetchEnd());
};
//...

const fetchStart = () => ({ type: "fetchStart" });
const fetchEnd = () => ({ type: "fetchEnd" });

const getUser = async (id) => {
  try {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/users/${id}`
    );
    const data = await response.json();
    return { type: "updateUser", payload: data };
  } catch (error) {
    console.error(error);
    return { type: "fetchError", payload: error };
  }
};
```

### 2.1 비슷하지만 다른 패턴

- 위에서 살펴본 두번째 패턴을 깔끔하게 사용하려면 액션 객체를 리턴하는 별도의 함수를 만드는 편이 일관성 측면에서 좋죠. 하지만 함수 만드는게 귀찮을 수도 있을거에요. 그럼 이렇게 쓰면 안 될까요?

```jsx
//...
const onClick = async () => {
  dispatch({ type: 'fetchStart' });
  dispatch({ type: 'getUser', payload: await getUser(id)) });
  dispatch({ type: 'fetchEnd' });
};
//...
```

- 안 되는건 아니지만 썩 좋지 않다고 생각해요. 왜냐하면 에러처리가 복잡해지기 때문이에요.
- 이렇게 사용하면 비동기 함수 이후에 dispatch 되는 액션의 타입이 "getUser"로 고정되어버려요. 따라서 reducer 내부에서 payload의 값에 따라 에러처리를 해줘야하는 상황이 되어버려요. 상상만해도 이상하죠?

## React-redux의 비동기 다루는 패턴 따라하기

React-redux를 사용하게 되면 리덕스 미들웨어를 이용해서 비동기를 다루는게 일반적이죠. 원래는 리덕스 미들웨어에서 비동기를 다루는 패턴을 소개하고 그 방식을 흉내낸 커스텀 hook을 만들려고 했는데, 글을 작성하다보니 순수 useReducer 만으로도 충분한 것 같다는 생각이 들어서 갑자기 의욕이 사라졌어요.
그래도 이대로 끝내기엔 아쉬우니까 어떤 형태인지 간단하게 소개만하고 마무리할게요.

### 사용 패턴

```javascript
//1. 객체 리터럴로 사용
const onClick = async () => {
  dispatch({ type: "fetchStart" });
  await dispatch({ type: "getUser", payload: id });
  dispatch({ type: "fetchEnd" });
};

//2. 액션 객체를 리턴하는 함수를 호출해서 사용
const onClick = async () => {
  dispatch(fetchStart());
  await dispatch(getUser(id));
  dispatch(fetchEnd());
};

async function getUser(id) {
  //...
  return { type: "updateUser", payload: data };
}
```

리덕스 미들웨어인 redux-thunk나 redux-saga를 사용하게 되면 dispatch 함수에 객체를 전달하는 형태로 사용하게 되는데요, 비동기 작업이라 하더라도 그냥 객체를 전달하고, 미들웨어를 작성하면 비동기 로직을 다룰 수 있어요.  
순수 useReducer로 비동기를 다루게 되면, `이벤트 발생 > 비동기 작업 > dispatch > 상태 변경(reducer) > 렌더링`의 순서로 데이터가 흐르게 돼요.  
반면, 리덕스 미들웨어를 사용하면 비동기 작업의 순서를 dispatch 이후로 옮길 수 있어요. `이벤트 발생 > dispatch > 비동기 작업 > 상태 변경 > 렌더링` 이렇게요. dispatch를 통해 모든 상태 변경이 시작되는 것을 선호하는 경우에는 이러한 형태를 사용할 수 있어요.  
이 형태를 한번 따라해보도록 할게요.

### 커스텀 useReducer 수정

useReducer는 원래 위와 같은 형태로 사용하지 못하지만 지난 글에서 만든 커스텀 useReducer를 아래와 같이 수정하면 위 패턴 중에 2번 패턴처럼 사용할 수 있어요.  
1번 패턴이 가능하도록 수정할 수도 있지만 조금 복잡해지기 때문에 여기서는 설명하지 않습니다.

```javascript
const useReducer = (reducer, initialState) => {
  const [state, setState] = useState(initialState);

  const updateState = (action) => setState((state) => reducer(state, action));

  const dispatch = (action) =>
    action instanceof Promise ? action.then(updateState) : updateState(action);

  return [state, dispatch];
};
```

- *getUser*와 같은 비동기 함수가 호출되면 그 함수의 리턴 값은 프로미스 객체에요.
- 따라서 _dispatch(getUser(id))_ 와 같이 사용하면 dispatch가 받는 인자는 프로미스 객체가 되지요.
- dispatch 함수의 인자가 프로미스이면, 프로미스가 resolved된 이후에 *getUser(id)*의 결과 값을 reducer에 전달해야해요.
- 이러한 동작은 *Promise.then()*메서드를 통해 할 수 있어요.
- _updateState_ 함수는 프로미스의 결과값을 받아서 실행되고 그러면 상태가 업데이트 돼요.
- dispatch 함수에 async/await을 사용하지 않은 이유는 action인자가 프로미스인 경우에만 비동기로 동작하기 위해서에요. 프로미스가 아닌 경우는 기존과 동일하게 동기적으로 동작하도록요.
- dispatch가 비동기 액션을 받는 경우, dispatch함수는 프로미스를 리턴하게 돼요.
- 따라서 dispatch를 호출하는 쪽에서는 await 키워드를 통해 비동기 dispatch가 완료되기를 기다릴 수 있게 됩니다.

## 닫는 글

여기까지 useReducer로 비동기를 다루는 여러가지 패턴에 대해 살펴봤어요.  
이 글에서는 간단한 비동기 호출만 다루었는데, 다음 글에서는 비동기의 결과에 따라 또다른 비동기 작업이 필요한 경우처럼 조금 복잡한 비동기를 다루는 팁을 소개해드릴게요.  
저도 아직 배우는 입장이라 리액트의 높은 자유도 때문에 머리가 아파오기도 하지만, 충분히 활용해서 이것저것 시도해보는게 성장을 위한 발판이 된다고 생각해요.  
보다 일관성 있고 읽기 좋은 코드를 고민하는 분들에게 도움이 되었으면 좋겠습니다. 👋  
(참고로, 1번, 2번 패턴을 모두 쉽게 사용할 수 있도록 제가 작성한 [useMiddleware](https://www.npmjs.com/package/react-use-middleware-hook) 라는 custom hook이 있답니다. 관심있으신 분들은 한번 살펴보세요!😊)
