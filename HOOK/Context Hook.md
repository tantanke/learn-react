# Context HOOK

获取上下文
更加的简洁

```js
 const ctx = React.createContext();

// function Test() { 
//     return <ctx.Consumer>
//         {value => <h1>Test，上下文的值：{value}</h1>}
//     </ctx.Consumer>
// }

function Test() {
    const value = useContext(ctx);
    return <h1>Test，上下文的值：{value}</h1>
}

export default function App() {
    return (
        <div>
            <ctx.Provider value="abc">
                <Test />
            </ctx.Provider>
        </div>
    )
}

```