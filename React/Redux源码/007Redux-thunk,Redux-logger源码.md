---
title:  Redux thunk源码
date: 2017-05-24 12:36:00
categories: redux
tags : redux
comments : true 
updated : 
layout : 
---

### 1 依赖：applyMiddleware源码

```javascript
export default function applyMiddleware() {
  for (var _len = arguments.length, middlewares = Array(_len), _key = 0; _key < _len; _key++) {
    middlewares[_key] = arguments[_key];
  }

  return function (createStore) {
    //enhancer(createStore)(reducer, preloadedState);这里的enhancer就是applyMiddleware(thunk,logger)
    //enhancer(createStore)返回值就是下面这个函数；
    return function (reducer, preloadedState, enhancer) {
      //还是先创建一个store;
      var store = createStore(reducer, preloadedState, enhancer);
      //下面对创建的store中的dispatch进行改造；
      var _dispatch = store.dispatch;
      var chain = [];

      var middlewareAPI = {
        getState: store.getState,
        dispatch: function dispatch(action) {
          return _dispatch(action);
        }
      };
      //放入到chain数组中的函数都形成了闭包；
      chain = middlewares.map(function (middleware) {
        //在这里对thunk和logger
        return middleware(middlewareAPI);
      });
      //将_dispatch指向新的函数；传入的store.dispatch还是原始的dispatch;
      _dispatch = compose.apply(undefined, chain)(store.dispatch);
      //_dispatch = chain[0](chain[1](store.dispatch))；
      //chain[1](store.dispatch) 返回值是一个函数，该返回值作为chain[0]的参数；
      //chain[0](chain[1](store.dispatch)) 的返回值是在下面源码处有注释
//返回最后的store
      return _extends({}, store, {
        dispatch: _dispatch
      });
    };
  };
}
```

### 2 redux-thunk源码

```javascript
'use strict';

exports.__esModule = true;
function createThunkMiddleware(extraArgument) {
  //下面返回函数其实就是thunk;
  return function (_ref) {
    //_ref就是middleWareAPI
    var dispatch = _ref.dispatch,
        getState = _ref.getState;
    //下面就是middleware(middlewareAPI);的返回值，被放到chain数组中,chain[0]，形成闭包；
    return function (next) { //这里的next就是chain[1](store.dispatch);
      return function (action) {  //这里就是 chain[0](chain[1](store.dispatch)) 也就是最终的dispatch,所以最终可以dispatch(action);
        if (typeof action === 'function') {
          //传给异步任务的dispatch还是最原始createStore里面定义的dispath;
          return action(dispatch, getState, extraArgument);
        }
        //如果action不是函数，那么就执行next，就是chain[1](store);
        return next(action);
      };
    };
  };
}

var thunk = createThunkMiddleware();
thunk.withExtraArgument = createThunkMiddleware;

exports['default'] = thunk;
```

什么是thunk

```
// Meet thunks.
// A thunk is a function that returns a function.
// This is a thunk.
```

### 3 Redux-logger源码

```javascript
function createLogger() {
 //......删除了一些判断代码；
  return function (_ref) {
   // _ref就是middleWareAPI
    var getState = _ref.getState;
    //chain[1]其实就是这个函数；形成闭包；
    return function (next) {
      //chain[1](store.dispatch)就是下面这个函数；
      return function (action) {
        console.log('dispatch 前：', getState());
        var returnedValue = next(action);
        console.log('dispatch 后：', getState(), '\n');
        return returnedValue;
      };
    };
  };
}
//这里的{dispatch,getState}其实就是applyMiddleware中的
/*
      var middlewareAPI = {
        getState: store.getState,
        dispatch: function dispatch(action) {
          return _dispatch(action);
        }
      };
*/
//下面返回函数其实就是logger;
const defaultLogger = ({ dispatch, getState } = {}) => {
  if (typeof dispatch === 'function' || typeof getState === 'function') {
    //下面就是middleware(middlewareAPI);的返回值，被放到chain数组中,chain[1]
    return createLogger()({ dispatch, getState });
  }
}
export {defaultLogger as logger} 
```

上面两者被放到chain数组中的函数其实都是类似于这样的

```javascript
return function (next) {
      return function (action) {
      };
    };
```

### 4 logger最简化代码

```javascript
function logger(middlewareAPI) {
  //像这样的代码，传入的参数会作为logger函数作用域中的局部变量，logger内部返回一个函数的时候，就会形成一个闭包r
  return function (next) {
    return function (action) {
      console.log('dispatch 前：', middlewareAPI.getState());
      var returnValue = next(action);
      console.log('dispatch 后：', middlewareAPI.getState(), '\n');
      return returnValue;
    };
  };
}
```

### 5 结合applyMiddleware改进dispatch；

```jsx
//createStore源码中有这么一行代码
enhancer(createStore)(reducer, preloadedState);这里的enhancer就是applyMiddleware(thunk,logger)
//============
const store = createStore(
    reducer,
    applyMiddleware(thunk, logger)
);
ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>, document.getElementById('app'));
```

* 先来看下如何dispatch异步action,也就是说action是一个异步函数;

从增强之后的_dispatch可以看到，所谓异步action,就是dispatch(action)中的action是一个函数；而这个函数就是一个异步请求之类的；

```javascript
//异步函数
function doFetch(url,data,type,actionTYPE){
  //_dispatch(doFetch(url,data,type,actionTYPE)) 中doFetch(url,data,type,actionTYPE)的返回值就是下面这个函数；这个函数就是thunk源码中,如下部分action;
  /*
  if (typeof action === 'function') {
          //传给异步任务的dispatch还是最原始createStore里面定义的dispath;
          return action(dispatch, getState, extraArgument);
        }*/
    return function(dispatch,getState){
        if(type === "POST"){
            return fetch(
            	//请求参数配置
            ).then({
                //请求成功处理函数
              dispatch(actionTYPE+"SUCCESS")
            }).then({
                //请求失败处理函数
              dispatch(actionTYPE+"FAILED")
            })
        }
    }
}
```

```javascript
_dispatch(doFetch(url,data,type,actionTYPE));
这里就是分发了一个异步函数
```

### 6 以上：

* 每个函数中的参数会作为该函数作用域中的一部分，如果该函数返回了另外一个函数，则会形成闭包
* 最后增强的_dispatch和最原始的dispatch完全是两回事，不要混淆了；



[redux-thunk](https://github.com/gaearon/redux-thunk)