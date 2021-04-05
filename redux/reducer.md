# Reducer
Reducer是用于改变数据的函数

1. 一个数据仓库，有且仅有一个reducer，并且通常情况下，一个工程只有一个仓库，因此，一个系统，只有一个reducer

2. 为了方便管理，通常会将reducer放到单独的文件中。

3. reducer被调用的时机
    - 通过store.dispatch，分发了一个action，此时，会调用reducer
    - 当创建一个store的时候，会调用一次reducer
        - 可以利用这一点，用reducer初始化状态
        - 创建仓库时，不传递任何默认状态
        - 将reducer的参数state设置一个默认值

4. reducer内部通常使用switch来判断type值
```js
export default (state = initialState, { type, payload }) => {
    switch (type) {
        case SETLOGINUSERTYPE:
            return payload
        default:
            return state
    }
}
```
5. reducer必须是一个没有副作用的纯函数
    - 为什么需要纯函数
        - 纯函数有利于测试和调式
        - 有利于还原数据
        - 有利于将来和react结合时的优化
    - 具体要求
        - 不能改变参数，因此若要让状态变化，必须得到一个新的状态
        - 不能有异步
        - 不能对外部环境造成影响（dom操作，计时器，设置localStorage）

6. 由于在大中型项目中，操作比较复杂，数据结构也比较复杂，因此，需要对reducer进行细分。
    - redux提供了方法，可以帮助我们更加方便的合并reducer
    - combineReducers: 合并reducer，得到一个新的reducer，该新的reducer管理一个对象，该对象中的每一个属性交给对应的reducer管理。

    细分方法：

## 不使用api
```js
function todos(state = [], action) {
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case TOGGLE_TODO:
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: !todo.completed
          })
        }
        return todo
      })
    default:
      return state
  }
}

function visibilityFilter(state = SHOW_ALL, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return action.filter
    default:
      return state
  }
}
```
index.js中
```js
function todoApp(state = {}, action) {
  return {
    visibilityFilter: visibilityFilter(state.visibilityFilter, action), // 这里实际上会返回一个数据
    todos: todos(state.todos, action) // 这里实际上会返回一个数据
    // 每一次使用的数据都是上一次返回的结果
  }
}
const store = createStore(todoApp)
```

1. 首先执行一遍这个reducer（todoApp）得到一个store
2. 然后第一次在执行的时候会执行对应的属性，初始化一个默认值
3. 后面每一次dispatch的时候，只需要传入对应的action，然后把action传递给每一个属性，如果action符合那么就会发生改变，不变就返回state

**这里的属性需要是一个reducer**

## 使用api combineReducers()

与下方的写法等价

```js
import { combineReducers } from 'redux'

const todoApp = combineReducers({
  visibilityFilter,
  todos
})

export default todoApp
```

可以设置别名
```js
export default function todoApp(state = {}, action) {
  return {
    visibilityFilter1: visibilityFilter(state.visibilityFilter1, action),
    todos1: todos(state.todos1, action)
  }
}
```
