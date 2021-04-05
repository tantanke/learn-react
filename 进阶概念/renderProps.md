# render props

有时候，某些组件的各种功能及其处理逻辑几乎完全相同，只是显示的界面不一样，可以使用以下的方案来解决重复代码的问题

1. HOC 高阶组件

2. render props
   - 某个组件，需要某个属性
   - 该属性是一个函数，函数的返回值用于渲染
   - 函数的参数会传递为需要的数据
   - 纯组件的属性尽量避免每次传递的函数的地址不一致
   - 通常传递的属性是 render

例如以下两个组件

第一个组件我们让页面上的`div`元素 随着我们鼠标的变化而变化
因此我们动态计算出 x，y 的值

```js
export default class MovablePanel extends PureComponent {
  state = {
    x: 0,
    y: 0,
  };

  divRef = React.createRef();

  handleMouseMove = (e) => {
    //更新x和y的值
    const { left, top } = this.divRef.current.getBoundingClientRect();
    const x = e.clientX - left;
    const y = e.clientY - top;
    this.setState({
      x,
      y,
    });
  };

  render() {
    return (
      <div
        ref={this.divRef}
        className="point"
        onMouseMove={this.handleMouseMove}
      >
        <div
          style={{
            width: 100,
            height: 100,
            background: "#008c8c",
            position: "absolute",
            left: this.state.x - 50,
            top: this.state.y - 50,
          }}
        ></div>
      </div>
    );
  }
}
```

第二个组件我们只是把鼠标的位置展示出来

```js
export default class ShowMousePoint extends PureComponent {
  state = {
    x: 0,
    y: 0,
  };

  divRef = React.createRef();

  handleMouseMove = (e) => {
    //更新x和y的值
    const { left, top } = this.divRef.current.getBoundingClientRect();
    const x = e.clientX - left;
    const y = e.clientY - top;
    this.setState({
      x,
      y,
    });
  };

  render() {
    return (
      <div
        ref={this.divRef}
        className="point"
        onMouseMove={this.handleMouseMove}
      >
        <h2>
          鼠标x：{parseInt(this.state.x)}， 鼠标y：{parseInt(this.state.y)}
        </h2>
      </div>
    );
  }
}
```

可以发现这两个组件只有在渲染的时候是不同的，其计算当前坐标的值的逻辑完全相同，只是显示的界面不一样，这种情况下我们就可以使用 render props 或者 HOC

## 使用 renderprops

把相同的计算逻辑抽离在一个组件中
对于显示不做处理

```js
export default class renderManage extends PureComponent {
  state = {
    x: 0,
    y: 0,
  };

  divRef = React.createRef();

  handleMouseMove = (e) => {
    //更新x和y的值
    const { left, top } = this.divRef.current.getBoundingClientRect();
    const x = e.clientX - left;
    const y = e.clientY - top;
    this.setState({
      x,
      y,
    });
  };

  render() {
    return (
      <div
        ref={this.divRef}
        className="point"
        onMouseMove={this.handleMouseMove}
      >
        this.props.render() // 你设定的属性名
      </div>
    );
  }
}
```

在外面调用的时候

```js

handerMouseMove = (mouse) => {
    return (
        <div
      style={{
        width: 100,
        height: 100,
        background: "#008c8c",
        position: "absolute",
        left: mouse.x - 50,
        top: mouse.y - 50,
      }}
    ></div>
    )
}
<renderManage render = {this.handerMouseMove}> // 传递一个函数
</renderManage>
```

也就是说```renderManage```只负责监听数据，提供数据，怎么渲染取决于外层传递的函数，
    - 这里的属性名你想是啥就是什么

可以直接写在组件内部，调用children 效果都是一样的
```js
<renderManage> // 传递一个函数
{this.handerMouseMove}
</renderManage>
```