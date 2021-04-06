# 原理

简单说一句就是：thunk这个中间件，就是发现有action是函数的话就会调用这个中间件来执行，否则就会向下传递                                         

# 源码

根据上面的源码，简单写一个

```js
function createThunkMiddleware(extra) {
    //该函数返回一个thunk中间件
    return store => next => action => {
        if (typeof action === "function") {
            // 统一返回值，让所有的都有返回值
            return action(store.dispatch, store.getState, extra);
        }
        return next(action);
    }
}

const thunk = createThunkMiddleware();
thunk.withExtraArgument = createThunkMiddleware; // 传递一个额外参数
export default thunk;
```

没错
你没看错
完了
完了......
我觉得就这样