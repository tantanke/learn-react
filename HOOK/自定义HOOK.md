# 自定义HOOK

通过对effectHook和stateHook的二次封装

例如:
1. 很多组件都需要在第一次加载完成后，获取所有学生数据
自定义字节的hook

在组件挂载的时候只运行一次的自定义hook

```js
import { useEffect, useState } from "react"
import { getAllStudents } from "../services/student"

/**
 * 当组件首次加载完成后，获取所有学生数据
 */
export default function useAllStudents() {
    const [students, setStudents] = useState([])
    useEffect(() => {
        (async ()=>{
            const stus = await getAllStudents();
            setStudents(stus);
        })();
    }, [])
    return students;
}
```

主函数中
```js
function Test() {
    const stus = useAllStudents();
    const list = stus.map(it => <li key={it.id}>{it.name}</li>)
    return <ul>
        {list}
    </ul>
}

export default function App() {
    return (
        <div>
            <Test />
        </div>
    )
}
```
依赖于其他数据的自定义hook

```js
/**
 * 根据页码和页容量获取学生数据，得到一个响应结果
 * 并且，当页码和页容量变化时，将重新获取学生数据（渲染页面）
 */
export default function useAllStudents(page = 1, limit = 10) {
    const [resp, setResp] = useState()
    useEffect(() => {
        (async () => {
            const resp = await getStudents(page, limit);
            setResp(resp);
        })();
    }, [page, limit])
    return resp;
}
```

比起类组件的生命周期，函数组件更加的灵活，可以更加方便的抽离逻辑，当然对于函数组件而言，高阶组件和renderprops也可以抽离逻辑，但是比起hooks，稍显笨重

很多组件都需要在第一次加载完成后，启动一个计时器，然后在组件销毁时卸载

```js
/* eslint "react-hooks/exhaustive-deps": "off" */
import { useEffect } from "react"

/**
 * 组件首次渲染后，启动一个Interval计时器
 * 组件卸载后，清除该计时器
 */
export default (func, duration) => {
    useEffect(() => {
        const timer = setInterval(func, duration)
        return () => {
            clearInterval(timer);
        }
    }, [])
}
```