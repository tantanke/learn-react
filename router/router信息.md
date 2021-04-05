# 路由信息
Router组件会创建一个上下文，并且，向上下文中注入一些信息

该上下文对开发者是隐藏的，Route组件若匹配到了地址，则会将这些上下文中的信息作为属性传入对应的组件

以下所有的参数都存放在props中

## history
它并不是window.history对象，我们利用该对象无刷新跳转地址

### 为什么没有直接使用history对象

1. React-Router中有两种模式：Hash、History，如果直接使用window.history，只能支持一种模式
2. 当使用windows.history.pushState方法时，没有办法收到任何通知，将导致React无法知晓地址发生了变化，结果导致无法重新渲染组件
* push：将某个新的地址入栈（历史记录栈）
    - 参数1：新的地址
    - 参数2：可选，附带的状态数据
    - 这两个函数的地址都是相对地址
* replace：将某个新的地址替换掉当前栈中的地址
* go: 与window.history一致
* forward: 与window.history一致
* back: 与window.history一致0
```js
function A(props) {
    return <div>
        <p>组件A</p>
        <button onClick={() => {
            props.history.replace("/b", "状态数据");
        }}>跳转到/b</button>
    </div>
}

function B(props) {
    return <div>
        <p>组件B</p>
        <p>
            获取状态数据：{props.history.location.state}
        </p>
        <button onClick={() => {
            props.history.replace("/a");
        }}>跳转到/a</button>
    </div>
}

function NotFound() {
    return <h1>找不到页面</h1>
}
export default function App() {
    return (
        <Router>
            <Switch>
                <Route path="/a" component={A} />
                <Route path="/b" component={B} />
                <Route component={NotFound} />
            </Switch>
        </Router>
    )
}
```
## location
与history.location完全一致，是同一个对象，但是，与window.location不同

location对象中记录了当前地址的相关信息

我们通常使用第三方库```query-string```，用于解析地址栏中的数据

```js
function A(props) {
    console.log(props.location)
    var query = qs.parse(props.location.search)
    var hash = qs.parse(props.location.hash);
    return <div>
        <p>组件A</p>
        <p>
            访问地址：{props.location.pathname}
        </p>
        <p>
            地址参数：a:{query.a}, b:{query.b}, c:{query.c}
        </p>
        <p>
            hash: d:{hash.d}, f:{hash.f}
        </p>
    </div>
}

function NotFound() {
    return <h1>找不到页面</h1>
}
export default function App() {
    return (
        <Router>
            <Switch>
                <Route path="/a" exact component={A} />
                <Route component={NotFound} />
            </Switch>
        </Router>
    )
}
```
## match
该对象中保存了，路由匹配的相关信息

* isExact：事实上，当前的路径和路由配置的路径是否是精确匹配的，和exact写没写没什么关系
* params：获取路径规则中对应的数据
* url：真实路径
* path：写在对应的Route中的path,表示匹配规则
实际上，在书写Route组件的path属性时，可以书写一个```string pattern（字符串正则）```

react-router使用了第三方库：Path-to-RegExp，该库的作用是，将一个字符串正则转换成一个真正的正则表达式。

```js
function News(props) {
    console.log(props.match)
    return <div>
        <p>显示{props.match.params.year}年{props.match.params.month}月{props.match.params.day}日的新闻</p>
    </div>
}

function NotFound() {
    return <h1>找不到页面</h1>
}
export default function App() {
    return (
        <Router>
            <Switch>
                <Route path="/news/:year/:month/:day" component={News} />
                /*会自动匹配符合该路径参数的表达式，并且存放在params中*/
                <Route component={NotFound} />
            </Switch>
        </Router>
    )
}
```

也可以让变量可传可不传
```js
 <Route path="/news/:year?/:month?/:day?" component={News} />
```
使用```*```号代表任意字符
```js
 <Route path="/news/:year?/:month?/:day?/*" component={News} />
```
进一步使用正则进行限制
```js
<Route path="/news/:year(正则)/:month(正则)/:day(正则)" component={News} />
```

向某个页面传递数据的方式：

1. 使用state：在push页面时，加入state
2. 利用search：把数据填写到地址栏中的？后 **常用**
3. 利用hash：把数据填写到hash后
4. params：把数据填写到路径中 **常用**

## 非路由组件获取路由信息
某些组件，并没有直接放到Route中，而是嵌套在其他普通组件中，因此，它的props中没有路由信息，如果这些组件需要获取到路由信息，可以使用下面两种方式：

1. 将路由信息从父组件一层一层传递到子组件
2. 使用react-router提供的高阶组件withRouter，包装要使用的组件，该高阶组件会返回一个新组件，新组件将向提供的组件注入路由信息

```js
class ShowTheLocation extends React.Component {
  static propTypes = {
    match: PropTypes.object.isRequired,
    location: PropTypes.object.isRequired,
    history: PropTypes.object.isRequired
  };

  render() {
    const { match, location, history } = this.props;

    return <div>You are now at {location.pathname}</div>;
  }
}
const ShowTheLocationWithRouter = withRouter(ShowTheLocation);
```