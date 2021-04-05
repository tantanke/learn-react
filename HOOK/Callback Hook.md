# Callback HOOK

如果对于回调函数(例如点击事件)，每一次都会重新创建该函数，会导致传递的属性发生变化

函数的地址每次渲染都发生了变化，导致了子组件跟着重新渲染，若子组件是经过优化的组件，则可能导致优化失效

和类组件一样的意思，需要在外重新定义，类组件可以设置为一个属性，函数组件使用useCallback

```js
class Test extends React.PureComponent {

    render() {
        console.log("Test Render")
        return <div>
            <h1>{this.props.text}</h1>
            <button onClick={this.props.onClick}>改变文本</button>
        </div>
    }
}

function Parent() {
    console.log("Parent Render")
    const [txt, setTxt] = useState(1)
    const [n, setN] = useState(0)
    const handleClick = useCallback(() => {
        setTxt(txt + 1)
    }, [txt])

    return (
        <div>
            {/* 函数的地址每次渲染都发生了变化，导致了子组件跟着重新渲染，若子组件是经过优化的组件，则可能导致优化失效 */}
            <Test text={txt} onClick={handleClick} />
            <input type="number"
                value={n}
                onChange={e => {
                    setN(parseInt(e.target.value))
                }}
            />
        </div>
    )
}

export default function App() {

    return (
        <div>
            <Parent />
        </div>
    )
}
```

写法：
该函数有两个参数：

函数，useCallback会固定该函数的引用，只要依赖项没有发生变化，则始终返回之前函数的地址
数组，记录依赖项
该函数返回：引用相对固定的函数地址

```js
const handleClick = useCallback(() => {
        setTxt(txt + 1)
    }, [txt])
```