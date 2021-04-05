一个基本例子

1. reducer本质上是一个函数，参数为state和action，返回值就是新设置的state

2. 使用reducer来初始化一个store，并且传入对应的默认值

3. 数据发生改变的时候使用dispatch来提交action，reducer处理之后通知store改变

例如
```js
function reducer(state, action) {
    //返回一个新的状态
    if (action.type === 'INCREASE') {
        return state + 1;
    }
    else if (action.type === 'DECREASE') {
        return state - 1;
    }
    else if (action.type === 'SET') {
        return action.payload;
    }
    return state;//如果是一个无效的操作类型，数据不变
}
const store = createStore(reducer, 10);
store.dispatch({{type:'INCREASE'});
console.log(store.getState()); //得到仓库中当前的数据
```

# Action  

1. action是一个plain-object（平面对象）
    - 它的__proto__指向Object.prototype

2. 通常，使用payload属性表示附加数据（没有强制要求）

3. action中必须有type属性，该属性用于描述操作的类型
    - 但是，没有对type的类型做出要求

4. 在大型项目，由于操作类型非常多，为了避免硬编码（hard code），会将action的类型存放到一个或一些单独的文件中(样板代码)

```js
export const INCREASE = Symbol("increase");
export const DECREASE = Symbol("decrease");
export const SET = Symbol("set");
```

5. 为了方面传递action，通常会使用action创建函数(action creator)来创建action

    - action创建函数应为无副作用的纯函数
        - 不能以任何形式改动参数
        - 不可以有异步
        - 不可以对外部环境中的数据造成影响

6. 为了方便利用action创建函数来分发（触发）action，redux提供了一个函数bindActionCreators，该函数用于增强action创建函数的功能，使它不仅可以创建action，并且创建后会自动完成分发。

例如
导出一堆action创建函数

```js
import * as actionTypes from "./action-type"
/**
 * 得到一个用于增加数字操作的action
 */
export function getIncreaseAction() {
    return {
        type: actionTypes.INCREASE
    };
}

export function getDecreaseAction() {
    return {
        type: actionTypes.DECREASE
    }
}

export function getSetAction(newNumber) {
    return {
        type: actionTypes.SET,
        payload: newNumber
    }
}
```

然后在主函数中使用
```js
//第一个参数，是action创建函数合并的对象，第二个参数是仓库的dispatch函数
//得到一个新的对象，新对象中的属性名与第一个参数的属性名一致
const boundActions = bindActionCreators(numberActions, store.dispatch);

//得到一个increase action，并直接分发
boundActions.getIncreaseAction(); //向仓库分发actio

```

可以看出这种写法的可读性更加的强，更加的简洁
