```js
export default function (actionCreators, dispatch) {
    if (typeof actionCreators === "function") {
        return getAutoDispatchActionCreator(actionCreators, dispatch);
    }
    else if (typeof actionCreators === "object") {
        const result = {}; //返回结果
        for (const key in actionCreators) {
            if (actionCreators.hasOwnProperty(key)) {
                const actionCreator = actionCreators[key]; //取出对应的属性值
                if (typeof actionCreator === "function") {
                    result[key] = getAutoDispatchActionCreator(actionCreator, dispatch);
                }
            }
        }
        return result;
    }
    else {
        throw new TypeError("actionCreators must be an object or function which means action creator")
    }
}

/**
 * 得到一个自动分发的action创建函数
 */
function getAutoDispatchActionCreator(actionCreator, dispatch) {
    return function (...args) {
        const action = actionCreator(...args)
        dispatch(action);
    }
}
```

返回一个相同的对象

使用如下
```js
const store = createStore(reducer);

const actionCreators = {
    addUser: createAddUserAction,
    deleteUser: createDeleteUserAction
}

const actions = bindActionCreators(actionCreators, store.dispatch)
// 传入对象就返回同一个对象
// 传入一个函数就会返回一个函数 

store.subscribe(() => {
    console.log("监听器1", store.getState());
})

actions.addUser({ id: 3, name: "abc", age: 111 }) // 传入对象
actions.deleteUser(3) // 传入对象
```