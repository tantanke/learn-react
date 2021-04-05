# 纯组件

react在默认情况下，父组件在更新的时候，默认会让所有的子组件都一起渲染

利用shouldComponentUpdate(nextProps,nextState)来和现在的来进行一个对比

PureComponent是一个组件，如果某个组件继承自该组件，则该组件在```shouldComponentUpdate(nextProps,nextState)```的生命周期的时候会对属性和状态进行一个浅比较，如果不一样就会```renturn false```

**注意**

1. PureComponent利用的是浅比较，所以对数组进行改变的时候直接使用push方法，地址不会发生改变，后续的比较对于数组的改变无法感知到
2. 为了效率应该尽量使用PureComponent，不要改变原来的状，永远都是创建新的状态
    - Immutable 不可变对象
3. 为一个元素绑定事件的时候不要直接绑定表达式，如果直接在属性中生命事件的回调函数，那么每一次渲染其父组件的时候都会重新创建一次这个事件处理函数，导致这个组件的重新渲染
    - 把事件处理函数定义成类组件的属性或者是函数组件的表达式

4. 第三方库 Immutable.js 只能创建不可变对象

5. 函数组件的纯组件使用，使用```React.memo()```
    - ```export default React.memo()```
    - 相当于就是直接写了一个高阶组件

```js

```
