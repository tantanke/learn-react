# memo Hook

```js
   const handleClick = useMemo(() => {
        return () => {
            setTxt(txt + 1)
        }
    }, [txt])

```
可以固定返回值，第一次调用的时候执行该hook，如果依赖没有变化，就一直重用该hook

callback是固定传入的函数，memo固定传入函数的返回值，泛用性更广

甚至可以返回一个数组节点

```js
import React, { useState, useMemo } from 'react'

function Item(props) {
    // console.log("Item Render " + props.value);
    return <li>{props.value}</li>
}


export default function App() {
    const [range,] = useState({ min: 1, max: 10000 })
    const [n, setN] = useState(0)
    const list = useMemo(() => {
        const list = [];
        for (let i = range.min; i <= range.max; i++) {
            list.push(<Item key={i} value={i}></Item>)
        }
        return list;
    }, [range.min, range.max])
    // const list = [];
    // for (let i = range.min; i <= range.max; i++) {
    //     list.push(<Item key={i} value={i}></Item>)
    // }
    return (
        <div>
            <ul>
                {list}
            </ul>
            <input type="number"
                value={n}
                onChange={e => {
                    setN(parseInt(e.target.value))
                }}
            />
        </div>

    )
}
```
也就是说如果React本身的引用都没有发生变化，那么一定不会重新渲染该组件