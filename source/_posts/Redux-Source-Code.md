title: Redux Source Code
author: Howard Wong
tags:
  - React
categories: []
date: 2018-02-07 11:46:00
---
# Redux

## Intro

```
repo: https://github.com/reactjs/redux
```

## API

- src/index.js

```javascript
import createStore from './createStore'
import combineReducers from './combineReducers'
import bindActionCreators from './bindActionCreators'
import applyMiddleware from './applyMiddleware'
import compose from './compose'
import __DO_NOT_USE__ActionTypes from './utils/actionTypes'

export {
  createStore,
  combineReducers,
  bindActionCreators,
  applyMiddleware,
  compose,
  __DO_NOT_USE__ActionTypes
}

```

## createStore

- src/createStore.js

```javascript
function createStore(reducer, preloadedState, enhancer) {
  // 判断如果没有传入 enhancer，则将 preloadedState 做为 enhancer
  if (typeof preloadedState === 'function' && typeof enhancer === 'undefined') {
    enhancer = preloadedState
    preloadedState = undefined
  }

  // enhancer 可以复写createStore函数
  // 一般enhancer是applyMiddleWare返回值
  if (typeof enhancer !== 'undefined') {
    if (typeof enhancer !== 'function') {
      throw new Error('Expected the enhancer to be a function.')
    }

    return enhancer(createStore)(reducer, preloadedState)
  }

  let currentReducer = reducer
  let currentState = preloadedState
  let currentListeners = []
  let nextListeners = currentListeners
  let isDispatching = false

  function ensureCanMutateNextListeners() {
    if (nextListeners === currentListeners) {
      nextListeners = currentListeners.slice()
    }
  }

  function getState() {
    return currentState
  }

  // store.subscribe(() =>
  //   console.log(store.getState())
  // )
  function subscribe(listener) {
    let isSubscribed = true

    ensureCanMutateNextListeners()
    nextListeners.push(listener)

    return function unsubscribe() {
      isSubscribed = false

      ensureCanMutateNextListeners()
      const index = nextListeners.indexOf(listener)
      nextListeners.splice(index, 1)
    }
  }

  // 传入action 覆盖currentState
  function dispatch(action) {
    if (isDispatching) {
      throw new Error('Reducers may not dispatch actions.')
    }

    try {
      isDispatching = true
      currentState = currentReducer(currentState, action)
    } finally {
      isDispatching = false
    }

    const listeners = (currentListeners = nextListeners)
    for (let i = 0; i < listeners.length; i++) {
      const listener = listeners[i]
      listener()
    }

    return action
  }

  function replaceReducer(nextReducer) {
    currentReducer = nextReducer
    dispatch({ type: ActionTypes.REPLACE })
  }

  // 测试API
  function observable() {
  }

  // When a store is created, an "INIT" action is dispatched so that every
  // reducer returns their initial state. This effectively populates
  // the initial state tree.
  dispatch({ type: ActionTypes.INIT })

  return {
    dispatch,
    subscribe,
    getState,
    replaceReducer,
    [$$observable]: observable
  }
}

```

## 中间件

- Middleware API

```
type MiddlewareAPI = { dispatch: Dispatch, getState: () => State }
type Middleware = (api: MiddlewareAPI) => (next: Dispatch) => Dispatch
```

- redux-thunk 简化版

```javascript
({ dispatch, getState }) => next => action => {
    if (typeof action === 'function') {
      return action(dispatch, getState);
    }
    return next(action);
  };
}
```

### 以redux-thunk为例
- src/applyMiddleware.js

```javascript
// 添加中间件，currying createStore 函数
export default function applyMiddleware(...middlewares) {
  return createStore => (...args) => {
    const store = createStore(...args)
    let dispatch = () => {
      throw new Error(
        `Dispatching while constructing your middleware is not allowed. ` +
          `Other middleware would not be applied to this dispatch.`
      )
    }
    let chain = []

    const middlewareAPI = {
      getState: store.getState,
      dispatch: (...args) => dispatch(...args)
    }
    // dispatch 目前是一个产生warn的空函数
    // 目的是在中间件初始化的时候阻止dispatch，并发出警告
    // 在完成chain之后，dispatch会在下一行替换
    // https://github.com/reactjs/redux/commit/dd165dfc6878bc9aa6045bc1fc1943640a23e5e8
    chain = middlewares.map(middleware => middleware(middlewareAPI))
    // 将数组reduce成chain
    dispatch = compose(...chain)(store.dispatch)

    return {
      ...store,
      dispatch
    }
  }
}

```

- 经过 applyMiddleware.apply(this, [ thunk ])
- dispatch 变成了

```javascript

// dispatch代表完整的中间件链，next代表下一层
// 当dispatch一个函数时
// 将完整的dispatch链做为参数传给中间件，然后执行函数（同步或异步）
// 因为传入的action变成object了，所以就会进入下一层
action => {
	if (typeof action === 'function') {
      return action(dispatch, getState);
    }
    return next(action);
};
```

- src/combineReducers.js

```
按照一级深度组合reducer
拼装成reducer标准格式
function combination(state = {}, action) {
}
当有action dispatch的时候
广播给所有的reducer并收集返回值combine成store
```
```javascript
function combineReducers(reducers) {
  const reducerKeys = Object.keys(reducers)
  const finalReducers = {}
  for (let i = 0; i < reducerKeys.length; i++) {
    const key = reducerKeys[i]

    if (typeof reducers[key] === 'function') {
      finalReducers[key] = reducers[key]
    }
  }
  const finalReducerKeys = Object.keys(finalReducers)

  let unexpectedKeyCache
  let shapeAssertionError
  try {
    assertReducerShape(finalReducers)
  } catch (e) {
    shapeAssertionError = e
  }

  return function combination(state = {}, action) {
    if (shapeAssertionError) {
      throw shapeAssertionError
    }

    let hasChanged = false
    const nextState = {}
    for (let i = 0; i < finalReducerKeys.length; i++) {
      const key = finalReducerKeys[i]
      const reducer = finalReducers[key]
      const previousStateForKey = state[key]
      const nextStateForKey = reducer(previousStateForKey, action)
      if (typeof nextStateForKey === 'undefined') {
        const errorMessage = getUndefinedStateErrorMessage(key, action)
        throw new Error(errorMessage)
      }
      nextState[key] = nextStateForKey
      hasChanged = hasChanged || nextStateForKey !== previousStateForKey
    }
    return hasChanged ? nextState : state
  }
}
```




