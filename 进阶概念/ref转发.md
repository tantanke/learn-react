# ref转发
Ref 转发是一个可选特性，其允许某些组件接收 ref，并将其向下传递（换句话说，“转发”它）给子组件。
举一个简单地例子

对于函数组件而言

```js
import React from 'react'

function A(props, ref) {
    return <h1 ref={ref}>
        组件A
        <span>{props.words}</span>
    </h1>
}

//传递函数组件A，得到一个新组件NewA
const NewA = React.forwardRef(A);

export default class App extends React.Component {

    ARef = React.createRef()

    componentDidMount() {
        console.log(this.ARef);
    }


    render() {
        return (
            <div>
                <NewA ref={this.ARef} words="asfsafasfasfs" />
                {/* this.ARef.current:  h1 */}
            </div>
        )
    }
}
```

1. 在react中ref，不能被当成相关的属性进行传递
2. 可以使用React.forwardRef来包裹一个函数组件A，那么这个函数组件的第二个参数就是当前的一个ref
3. 在使用React.forwardRef返回的新的组件的时候，这个高阶组件不会对ref进行处理，而是把参数传递给包装后的函数组件A
4. 函数组件A在内部就可以指定传入的ref指向谁

对于类组件而言，直接当成一个自定义的属性传入

```js
<NewA ref1={this.ARef} words="asfsafasfasfs" />
```

这样看起来不太行，更优雅一些，既然React.forwardRef只是说传入一个函数组件，那直接使用一个函数包装一下的话不是就满足要求了吗

```js
function logProps(Component) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props:', prevProps);
      console.log('new props:', this.props);
    }

    render() {
      const {forwardedRef, ...rest} = this.props;

      // 将自定义的 prop 属性 “forwardedRef” 定义为 ref
      return <Component ref={forwardedRef} {...rest} />;
    }
  }

  // 注意 React.forwardRef 回调的第二个参数 “ref”。
  // 我们可以将其作为常规 prop 属性传递给 LogProps，例如 “forwardedRef”
  // 然后它就可以被挂载到被 LogProps 包裹的子组件上。
  return React.forwardRef((props, ref) => { // 
    return <LogProps {...props} forwardedRef={ref} />;
  });
}
```
最常用的方式就是让高阶组件把ref转发给包装之前的普通组件

也就是说外面的组件在调用ref的时候，会执行这个被包装的函数式组件，然后把待确认的ref传递给这个箭头函数

箭头函数在执行的时候返回一个新的组件，通过约定好的属性值传入未绑定的ref

在这个返回的新的组件的内部，对于这个ref进行处理，实现一个对应的绑定

