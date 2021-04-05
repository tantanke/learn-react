# 新的api

在旧版的api中
多个组件都创建了上下文，该组件不会从自己的上下文中去取，只会从自己的父组件的上下文中去取
子组件中拿到的数据是最近一个父级的上下文中存在的数据，如果采用了函数进行修改，可能函数和上下文的数据不是来源于一个组件
找context一定是就近原则

**创建上下文**

上下文是一个独立于组件的对象，该对象通过React.creatContext（默认值）创建

返回的是一个包含两个属性的对象

1. Provider: 一个组件，该组件会创建一个上下文，作为上下文生产者，该组件有一个value属性来指定对应的上下文 （ctx.Provider）
2. Comsumer：消费者，函数组件之中需要使用Comsumer来获取上下文数据
**使用上下文的数据**

要求：必须拥有静态属性contextType，这个静态属性会被赋值为ctx，然后在子组件中直接使用this.context中调用，使用者和提供者都要使用

    - Provider不要进行复用，一个Provider和一个React.creatContext进行绑定
    - 提供者创建Provider组件，类组件使用者把上下文对象设置为静态属性contextType的值
    - 这种写法只能使用一个上下文的数据
```js
ctx = React.creatContext()
static contextType = ctx
this.context.xxx 
```
或者直接使用 ```Comsumer``` 来进行获取，这种方法在类组件和函数组件中都可以使用

```js
const ctx1 = React.createContext();
const ctx2 = React.createContext();

function ChildA(props) {
    return (
        <ctx2.Provider value={{
            a: 789,
            c: "hello"
        }}>
            <div>
                <h1>ChildA</h1>
                <h2>
                    <ctx1.Consumer>
                        {value => <>{value.a}，{value.b}</>}
                    </ctx1.Consumer>
                </h2>
                <ChildB />
            </div>
        </ctx2.Provider>);
}

class ChildB extends React.Component {

    render() {
        return (
            <ctx1.Consumer>
                {value => (
                    <>
                        <p>
                            ChildB，来自于上下文的数据：a: {value.a}, b:{value.b}
                            <button onClick={() => {
                                value.changeA(value.a + 2);
                            }}>后代组件的按钮，点击a+2</button>
                        </p>
                        <p>
                            <ctx2.Consumer>
                                {val => (
                                    <>
                                        来自于ctx2的数据：
                                        a: {val.a}， c:{val.c}
                                    </>
                                )}
                            </ctx2.Consumer>
                        </p>
                    </>
                )}
            </ctx1.Consumer>
        );
    }
}

export default class NewContext extends Component {

    state = {
        a: 0,
        b: "abc",
        changeA: (newA) => {
            this.setState({
                a: newA
            })
        }
    }

    render() {
        return (
            <ctx1.Provider value={this.state}>
                <div>
                    <ChildA />

                    <button onClick={() => {
                        this.setState({
                            a: this.state.a + 1
                        })
                    }}>父组件的按钮，a加1</button>
                </div>
            </ctx1.Provider>
        )
    }
}
```
1. 在类组件中直接使用this.context来获取上下文数据

2. 在函数组件之中需要使用Comsumer来获取上下文数据

    - Consumer是一个组件
    - 它的子节点是一个函数，它的props.children需要传递一个函数
    - 这个函数的参数value就是当前的上下文，返回值就是渲染的react元素
    - 需要在哪里使用就在哪里使用```ctx.Consumer```

```js
const ctx = React.createContext();

function ChildA(props) {
    return <div>
        <h1>ChildA</h1>
        <h2>
            <ctx.Consumer>
                {value => <>{value.a}，{value.b}</>}
            </ctx.Consumer>
        </h2>
        <ChildB />
    </div>
}
```

**多个上下文中应该怎么样处理**

用谁的数据就去拿谁的数据，不会像以前旧版的一样的就近原则

**注意细节**

如果，上下文提供者(Context.Provider)中的value属性发生变化(地址不一样)，setState会建立一个新对象来替换掉原来的对象，会导致该上下文提供的所有后代元素全部重新渲染，无论该子元素是否有优化

可以把原本的state放在state的一个属性中，setState只会改变这个state对象，不会改变这个state的一个属性

这种方式其实叫强制更新，这种方式会直接绕过shouldComponentUpdate

和vue的eventBus其实挺像的，但是比eventBus的粒度其实更加的细，层次也会更加的明显

**实例**

利用context来封装表单组件，在子表单中操作总表单的上下文数据，在父组件的上下文中提供对应的函数让子组件来对父组件中的上下文进行操作


