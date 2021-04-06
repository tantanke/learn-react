# 概述

其实在官方文档中，已经告诉我们如何一步一步的来封装一个我们专门的中间件了

这个中间件的原理其实在官方文档中已经说了很多了

那么我们简单实现一个

```js
export default function (...middlewares) {
    return function (createStore) { //给我创建仓库的函数
        //下面的函数用于创建仓库
        return function (reducer, defaultState) {
            //创建仓库
            const store = createStore(reducer, defaultState);
            let dispatch = () => { throw new Error("目前还不能使用dispatch") };
            const simpleStore = {
                getState: store.getState,
                dispatch: (...args) => dispatch(...args)
            }
            //给dispatch赋值
            //根据中间件数组，得到一个dispatch创建函数的数组
            const dispatchProducers = middlewares.map(mid => mid(simpleStore));
            dispatch = compose(...dispatchProducers)(store.dispatch);
            return {
                ...store,
                dispatch
            }
        }
    }
}
```