# imperativeHandle hook

useImperativeHandle 可以让你在使用 ref 时自定义暴露给父组件的实例值

类组件

```js
import React, { useRef } from 'react'

class Test extends React.Component {

    method() {
        console.log("Test method called");
    }

    render() {
        return <h1>Test Component</h1>
    }
}

export default function App() {
    const testRef = useRef();
    return (
        <div>
            <Test ref={testRef} />
            <button onClick={() => {
                testRef.current.method();
            }}>点击调用Test组件的method方法</button>
        </div>
    )
}
```

```js
import React, { useRef, useImperativeHandle } from 'react'

function Test(props, ref) {
    useImperativeHandle(ref, () => {
        //如果不给依赖项，则每次运行函数组件都会调用该方法
        //如果使用了依赖项，则第一次调用后，会进行缓存，只有依赖项发生变化时才会重新调用函数
        //相当于给 ref.current = 1
        return {
            method(){
                console.log("Test Component Called")
            }
        }
    }, [])
    return <h1>Test Component</h1>
}

const TestWrapper = React.forwardRef(Test)


export default function App() {
    const testRef = useRef();
    return (
        <div>
            <TestWrapper ref={testRef} />
            <button onClick={() => {
                testRef.current.method();
            }}>点击调用Test组件的method方法</button>
        </div>
    )
}
```