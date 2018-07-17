title: MiddleWare
date: 2017-11-12
tags: react
toc: true
categories: 前端框架
---

### React 中间件

> 在Redux 中为我们提供了一个 applyMiddleware 的接口函数，来处理我们需要使用的中间件；

**react 有那些中间件**
> redux-logger  --- 操作日志
> redux-thunk   --- 将 dispatch 的控制权交给开发者，允许在 action 中返回一个函数，接收一个 dispatch 参数，并且可以异步调用它
> redux-promise --- 支持 dispatch 一个异步 Promise action 并且等待结果后 dispatch 一个 action
> 中间件函数签名 ({ getState, dispatch }) => next => action
----

#### redux-logger

```
  let logger = store => dispatch => action=>{
    console.log(store.getState().number);
    dispatch(action);
    console.log(store.getState().number)
  };
  let applyMiddleWare = middleware => createStore => reducer =>{
    let store = createStore(reducer);
    let middle = middleware(store);
    let dispatch = middle(store.dispatch);
    return { //将中间返回的dispatch方法覆盖掉原有store中的dispatch
      ...store,
      dispatch
    }
  };
  export default applyMiddleWare(logger)(createStore)(reducer);
```

#### redux-thunk

```
  let reduxThunk = store => dispatch => action=>{
    if(typeof action === 'function'){ //如果是函数将派发的权限传递给函数
      return action(dispatch,store.getState);
    }
    dispatch(action);
  };
```

#### redux-promise

```
  let reduxPromise = store => dispatch => action=>{
    if(action.then){
      return action.then(dispatch); //只支持成功
    }
    else if(action.payload&&action.payload.then){
      // 如果payload是一个promise 会对成功和失败都进行捕获并且将成功或失败的数据放到payload中进行派发
      return action.payload.then(function (data) {
        dispatch({...action,payload:data});
      },function (data) {
        dispatch({...action,payload:data});
      })
    }
    dispatch(action);
  };
```

#### 实现 compose
> compose 也是 redux 提供的5个函数之一（**createStore**,**combineReducers**,**applyMiddleware**,bindActionCreators,compose）只不过我们最常用的就是加粗的3个函数；bindActionCreators,compose 是 redux 内部可以自主触发的函数，所以我们主动用到的机会比较少

```
// compose 的主要职责就是将传入的参数（func）： compose(funcA, funcB, funcC) 转换成 compose(funcA(funcB(funcC())))，你可以理解为把这些独立的函数'串联'到一起后执行；

function combineStr(str1,str2){
  return str1+str2;
}
function strLen(str){
  return str.length;
};


let compose = (...fns)=>(...args)=> {
  let last = fns.pop();
  return fns.reduceRight(function (prev,next) {
      return next(prev);
  },last(...args))
}; // 将上一个函数当做参数传给下一个函数；

console.log(compose(strLen, combineStr)('hello', ' world'));
```

#### 实现 combineReducers
> 原理非常的简单，将创建的多个 reducer 合并成一个 reducer，之前的 reducer 都变成新对象属性存在

```
let combineReducers = (reducers) => (newState = {}, action) => {
    for (let key in reducers) {
        newState[key] = reducers[key](newState[key], action)
    }
    return newState;
};
```

> 最终实现的结果：export default createStore(reducer,applyMiddleware(reduxThunk,reduxPromise));

> 基本上 redux 中的方法的思想已经明白了，redux 仅仅是 react 中的一部分，想更深入的了解 react 框架思想和原理，这是一个比较漫长的路，加油吧所有走在 react 的开发者们。