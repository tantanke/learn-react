# 插槽

vue里面的插槽其实和react里面中的children的属性更加的相近

插槽:将一个react元素渲染到指定的dom容器中

ReactDOM.createPortal(React元素,真实的DOM容器)，该函数返回一个组件
不输入第二个参数就和原来一样

    - 组件结构不会有变化，只会包装一层```ReactPortal```的高阶组件
    - 但是真实的dom元素会发生改变
    - 说明了虚拟dom结构和真实的dom结构可以是不相同的


**注意事件冒泡**

1. React中的事件是包装过的，它的事件冒泡是根据虚拟dom树来进行冒泡的，与真实的dom树无关
2. 就算我们使用插槽把子元素放到了另一个真实的dom中，react可以根据组件的虚拟dom来进行冒泡

用法
```js
function ChildA() {
    return ReactDOM.createPortal(<div className="child-a" style={{
        marginTop: 200
    }}>
        <h1>ChildA</h1>
        <ChildB />
    </div>, document.querySelector(".modal"));
}

function ChildB() {
    return <div className="child-b">
        <h1>ChildB</h1>
    </div>
}

export default function App() {
    return (
        <div className="app" onClick={e => {
            console.log("App被点击了", e.target)
        }}>
            <h1>App</h1>
            <ChildA />
        </div>
    )
}
```