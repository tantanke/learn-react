# ref

例如一个例子，我们点击一个按钮，让一个输入框聚焦

这个就像vue中的ref来帮助我们来使用dom元素
```js

this.refs.txt

<input ref='txt' value='111/>
```

场景:希望直接使用dom元素中的某一个方法，或者直接使用自定义组件中的方法

1. ref作用于内置的html组件，得到的将是一个dom对象属性.
2. 作用于自定义类组件可以得到组件的实例
3. ref不能来选中函数式组件

利用对象来创建ref

```js
constructor(){
    super(props)
    this.txt = React.creaRef()
    // 利用this.txt.current来进行调用
}

<input ref={this.txt} value='111/>
```

利用函数来创建ref

```js

constructor(){
    super(props)
    this.txt = React.creaRef()
    // 利用this.txt.current来进行调用
}

<input ref={el => {
    this.txt= el // 把el赋值给focus
}} value='111/>
```

 
函数的调用时间：

1. componentDidMount的时候会调用该函数
    - 在componentDidMount事件中可以使用ref
2. 如果ref的值发生了变动（旧的函数被新的函数替代），分别调用旧的函数以及新的函数，时间点出现在componentDidUpdate之前
    - 旧的函数被调用时，传递null
    - 新的函数被调用时，传递对象
    - 如果希望函数只被创建一次，那么可以直接把函数存为组件的一个属性
3. 如果ref所在的组件被卸载，会调用函数

谨慎使用ref

能够使用属性和状态进行控制，就不要使用ref。

调用真实的DOM对象中的方法
某个时候需要调用类组件的方法
