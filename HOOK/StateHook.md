# State Hook
State Hook是一个在函数组件中使用的函数（useState），用于在函数组件中使用状态

## useState

* 函数有一个参数，这个参数的值表示状态的默认值
* 函数的返回值是一个数组，该数组一定包含两项
    - 第一项：当前状态的值
    - 第二项：改变状态的函数
    - 使用状态就直接用，改变状态用第二个传入

```js
function ExampleWithManyStates() {
  // 声明多个 state 变量！
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
  // ...
}
```
一个函数组件中可以有多个状态，这种做法非常有利于横向切分关注点。

## 原理

第N次调用useState，就会把状态数组的第N-1项设置当前的值，注意每一次函数组件都会被重新创建，利用状态数组来保存1状态

利用一个状态数组来维持数据，每一次利用useState，都会查看对应的状态数据，有就直接复用，把对应的下标的值赋值给解构出的变量，没有就创建，使用函数来改变状态，其实是改变了状态数组里面的值 


## 注意的细节

1. useState最好写到函数的起始位置，便于阅读

2. useState严禁出现在代码块（判断、循环）中

3. useState返回的函数（数组的第二项），引用不变（节约内存空间）

4. 使用函数改变数据，若数据和之前的数据完全相等（使用Object.is比较），不会导致重新渲染，以达到优化效率的目的。

5. 使用函数改变数据，传入的值不会和原来的数据进行合并，而是直接替换。
    - setState采用的是合并的方式

6. 如果要实现强制刷新组件
    - 类组件：使用forceUpdate函数
    - 函数组件：使用一个空对象的useState

7. 如果某些状态之间没有必然的联系，应该分化为不同的状态，而不要合并成一个对象

8. 和类组件的状态一样，函数组件中改变状态可能是异步的（在DOM事件中），多个状态变化会合并以提高效率，此时，不能信任之前的状态，而应该使用回调函数的方式改变状态。如果状态变化要使用到之前的状态，尽量传递函数。

例如:
```js
export default function App() {
    console.log("App Render");
    const [visible, setVisible] = useState(true);
    const [n, setN] = useState(0);
    return <div>
        <p style={{ display: visible ? "block" : "none" }}>
            <button onClick={() => {
                setN(n - 1)
            }}>-</button>
            <span>{n}</span>
            <button onClick={() => {
                setN(n + 1)
            }}>+</button>
        </p>
        <button onClick={() => {
            setVisible(!visible);
        }}>显示/隐藏</button>
    </div>
}
```

