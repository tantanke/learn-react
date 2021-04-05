# Context

上下文， 表示做某一些事情的环境

解决什么问题，相当于就是vue的props，react的props只能支持一层一层的传递

1. 当某个组件创建了上下文后，上下文中的数据，会被所有的后代组件共享
2. 如果当某个组件依赖了上下文，会导致该组件不再纯粹，数据不再是全部来自属性和状态

## 旧的API

只有类组件才可以创建上下文

1. 给类组价书写静态属性 
```js
static childContextTypes = {
    a：PropTypes.string
}

```

2.添加实例方法，getChildContext
该方法返回的对象就是上下文之间的数据
里面的属性需要满足类型约束
```JS
getChildContext(){
    return ({
        
    })
}

```

**使用上下文中的数据**

在需要使用的地方声明上下文的数据,只能使用已经声明到的，声明必须一致
必须要有一个静态属性，不管是函数组件还是类组件
```js
// 子组件中

static contextTypes = {
    a:PropTypes.string
}

```

在组件的构造函数中通过第二个参数获得已经声明的上下文
但是构造函数只会执行一次，改变了就会获取不到
```js

constructor(props,context){
super(props,context)
}
```
直接在```this.context```中拿到数据

函数组件直接通过第二个参数得到第一个数据

```js
function child(props，context)
```

**上下文中的数据变化**

上下文之间的状态不允许重新发生变化

父组件需要注册和使用，子组件中也需要注册静态属性和使用

**子组件改变父组件**

在父组件的context中添加一个函数，子代可以调用该函数来改变父组件，类似于自定义事件的传递什么的