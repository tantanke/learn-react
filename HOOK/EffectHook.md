# Effect Hook

Effect Hook：用于在函数组件中处理副作用

## 副作用：

简单地来说就是函数的运行会对外部产生影响

1. ajax请求
2. 计时器
3. 其他异步操作
3. 更改真实DOM对象
4. 本地存储
5. 其他会对外部产生影响的操作
函数：useEffect，该函数接收一个函数作为参数，接收的函数就是需要进行副作用操作的函数

```js
export default function App() {
    const [n, setN] = useState(0)
    //以下代码属于副作用
    // document.title = `计数器：${n}`;

    useEffect(() => {
        console.log("改变页面标题的副作用操作")
        document.title = `计数器：${n}`;
    })
    useEffect(() => {
        console.log("其他的副作用操作")
    })
    return (
        <div>
            <span>{n}</span>
            <button onClick={() => {
                setN(n + 1);
            }}>+</button>
        </div>
    )
}
```

## 细节

1. 副作用函数的运行时间点，是在页面完成真实的UI渲染之后。因此它的执行是异步的，并且不会阻塞浏览器
    - 与类组件中componentDidMount和componentDidUpdate的区别
    - componentDidMount和componentDidUpdate，更改了真实DOM，但是用户还没有看到UI更新，同步的。
    - useEffect中的副作用函数，更改了真实DOM，并且用户已经看到了UI更新，异步的。

2. 每个函数组件中，可以多次使用useEffect，但不要放入判断或循环等代码块中。

3. useEffect中的副作用函数，可以有返回值，返回值必须是一个函数，该函数叫做**清理函数**
    - useEffect在组件销毁的时候是不会被重新调用的
    - 该函数运行时间点，在每次运行副作用函数之前
    - 首次渲染组件不会运行
    - 组件被销毁时一定会运行
```js
useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
```
4. useEffect函数，可以传递第二个参数,**依赖数组**

    - 第二个参数是一个数组

    - 数组中记录该副作用的依赖数据

    - 当组件重新渲染后，只有依赖数据与上一次不一样的时，才会执行副作用

    - 所以，当传递了依赖数据之后，如果数据没有发生变化
        - 副作用函数仅在第一次渲染后运行
        - 清理函数仅在卸载组件后运行
例如
```js
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // 仅在 count 更改时更新
```
特别的：使用空数组作为依赖项，则副作用函数仅在挂载的时候运行

```js
function Test() {
    useEffect(() => {
        console.log("副作用函数，仅挂载时运行一次")
        return () => {
            console.log("清理函数，仅卸载时运行一次")
        };
    }, []); //使用空数组作为依赖项，则副作用函数仅在挂载的时候运行
    console.log("渲染组件");
    const [, forceUpdate] = useState({})

    return <h1>Test组件 <button onClick={() => {
        forceUpdate({})
    }}>刷新组件</button></h1>
}
```
5. 副作用函数中，如果使用了函数上下文中的变量，则由于闭包的影响，会导致副作用函数中变量不会实时变化。

闭包的问题

每一次输出的n都是重新渲染之后依赖的n

```js
export default function App() {
    const [n, setN] = useState(0)
    useEffect(() => {
        setTimeout(() => {
            console.log(n); //n指向，当前App函数调用时的n
        }, 5000);
    })
    return (
        <div>
            <h1>{n}</h1>
            <button onClick={() => {
                setN(n + 1);
            }}>n+1</button>
        </div>
    )
}
```

6. 副作用函数在每次注册时，会覆盖掉之前的副作用函数，因此，尽量保持副作用函数稳定，否则控制起来会比较复杂。

例如以下的例子
```js
let n = 1;

function func1() {
    console.log("odd 副作用函数")
    return () => {
        console.log("odd 清理函数")
    }
}

function func2() {
    console.log("even 副作用函数")
    return () => {
        console.log("even 清理函数")
    }
}

export default function App() {
    const [, forceUpdate] = useState({})
    useEffect(n % 2 === 0 ? func2 : func1);
    n++;
    return (
        <div>
            <button onClick={() => {
                forceUpdate({});
            }}>强制刷新</button>
        </div>
    )
}
```

一句话总结：useEffect副作用函数，是在UI展示完成之后运行的一个异步函数，可以传入两个参数，第一个参数就是要执行的副作用函数，这个副作用函数一定会运行，这个函数的返回值会在下一次副作用执行之前完成，称之为清理函数，首次渲染的时候不会运行，卸载的时候一定会运行清理函数。第二个参数是数组，代表其依赖项（与useState或者props配合使用），依赖项不改变就不会运行副作用函数

渲染 -> 副作用 -> 清理函数 -> 渲染 ->清理函数->副作用