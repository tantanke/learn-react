# react中的事件

这里的事件指的是：React内置的DOM组件中的事件 onClick onFocus 什么的

1. 给document注册事件
2. 几乎所有的元素事件处理，都会在document的事件中处理
    - 有些事件处理不会冒泡，react也没法，onfocus ommouseenter 这个会先于document进行处理，直接在元素上监听
    - 有些document上没有的事件，类似于播放器上的事件
    
3. 在document的事件处理中，React会根据虚拟dom树完成事件函数的调用
4. React中的事件参数，并非真正的事件参数，而是已经被react包装以后的事件参数
    - e.nativeEvent 得到真实的dom对象 但是没什么用
    - e.nativeEvent.stopPropagation() 没啥用
    - e.stopPropagation() 阻止冒泡
    - e.nativeEvent.stopImmediatePropagation() // 阻止剩余的事件处理函数运行
在APP中
```js
 <button onClick={e => {
            console.log("react: 按钮被点击了");
             e.nativeEvent.stopImmediatePropagation()
        }}>按钮</button>
```
index中
```js
ReactDOM.render(<App />, document.getElementById('root'));
document.addEventListener("click", function(e){ // 在render的时候实际上会自动执行一次事件的添加 
    e.stopImmediatePropagation();//阻止剩余的事件处理程序运行
})

document.addEventListener("click", function (e) {
    console.log("真实DOM：document 被点击了");
})
```
5. 为了提高执行效率，React使用事件对象池来处理事件对象，同一个事件对象可能会被重用

第一行输出的是true

```js
export default function App() {
    return <div onClick={e => {
        console.log(prev === e);
        console.log("react: div被点击了")
    }}>
        <input type="text" onFocus={e => {
            console.log("react：文本获得了焦点")
        }} />
        <button onClick={e => {
            console.log("react: 按钮被点击了");
            prev = e;
        }}>按钮</button>
    </div>
}
```


```js
export default function App() {
    return <div onClick={e => {
        console.log(prev === e);
        console.log("react: div被点击了")
    }}>
    </div>
}

document.querySelector("#root").onclick = function(e){
    console.log("root被点击了")
    e.stopPropagation()// 这样会直接阻止react的元素的事件被触发
}

```

可以发现root被点击了是在这个react元素被点击了之前调用的

也就是说document的事件会先运行

然后在document的点击事件之中去利用事件冒泡的方法调用react之间的方法

**注意事项**

1. 如果给真实的DOM注册事件，阻止了事件冒泡，就会导致react的相应事件无法触发

2. 给真实的DOM注册事件，事件会先于react中运行

3. React中的元素阻止事件冒泡，无法阻止真实的DOM元素冒泡，因为React的事件都在document中被委托处理了，而不是处理的原生的

4. 事件会在真实的DOM中冒泡到document，然后在document中执行react中的事件冒泡

5. 可以通过e.nativeEvent.stopImmediatePropagation()阻止document上剩余事件上的运行，例如一些第三方插件

6. 不要异步的去重用事件对象，每个事件对象默认不会被持久化，每一次被调用之后就会被销毁掉
    - 可以使用 ```e.persist()```来进行一个事件持久化

