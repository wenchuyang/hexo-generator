---
title: redux使用指南
tags: [react, redux, react-redux]
date: 2021-03-27 11:40:47
categories:
- 计算机
- 前端
---

# 什么时候使用
redux 帮助我们进行状态（数据）管理。如果你可以不用，那么就没有必要用。
如果应用中有以下场景可以考虑使用：
1. 某个组件的状态需要共享
2. 某个组件需要在任何地方都可以拿到
3. 一个组件需要改变全局状态
4. 一个组件需要改变另一个组件的状态

# redux 思路
所有的变量和处理函数都放在 store 里。`var store = Redux.createStore(reducer)`
变量用 state 存储，处理函数是 action . reducer 是一个函数，接收旧的 state , 返回新的 state . 并且负责 state 的初始化和 action 的分类处理 . `reducer = function(state, action){...}`
action 参数是一个对象 `{type: "", payload: ""}` , 其中 type 表示 action 类型， payload 是 action 函数 的参数。
外部获取变量使用 `store.getState()` 拿到 state 的值。
`store.subscribe(render)` 用来监听 action ，一旦 action 被触发 (dispatch) 了，就执行 render 函数。 render 函数负责刷新页面。
`store.dispatch({ type: "add", payload: 1 })` 触发一个类型为 "add" 的 action ， 并且传入参数 1 。在需要调用的时候调用。

# 原生 js + redux
[原生 js + redux Demo](https://codesandbox.io/s/redux-with-js-uon9m?file=/src/index.js)
redux 作为一个状态管理工具，同样适用于原生 js 。这里用它实现一个点击加一的效果。
1. 在 index.html 里创建一个 id 为 app 的 div 作为根元素。
2. 安装 redux 包
3. 声明一个 reducer 函数，传递两个参数 state (数据) 和 action (类型和函数参数) 。
    ```
    var reducer = function (state, action) {
        state = state || {  // init state
            number: 0
        };
        switch (action.type) { // 事件处理
            case "add":
                return { number: state.number + action.payload };
            default:
                return state;
        }
    };
    ```
4. 根据 reducer 创建 store
   ```
    import { createStore } from "redux";
    var store = createStore(reducer);
   ```
5. 监听 store 里边的改动。`store.subscribe(render)`
6. 创建 render 函数
   ```
    function render() {
        let str = `
            <div id="display">${store.getState().number}</div>
                <button id="add1">点我加一</button>
        `;
        document.getElementById("app").innerHTML = str;
    }
   ```
7. 执行 render 函数，渲染页面。`render()`
8. 给 button 添加事件监听，在点击了按钮之后执行 store.dispatch ，发布时间。
   这里因为 render 的时候会重新弄一个 button 上去，并不是原先的 button 了，如果直接监听 button 的点击事件的话，那么这个函数只能执行一次。所以采用事件委托，监听根节点 #app 的点击事件，然后判断如果是在 button 上的点击则执行接下来的语句。
   ```
    document.getElementById("app").addEventListener("click", (e) => {
        if (e.target.id === "add1") {
            store.dispatch({ type: "add", payload: 1 });
        }
    });
   ```
至此效果完成。
可以看见我们创建了一个页面，然后给 button 添加了一个事件，如果点击了这个按钮，就发布（ dispatch ） 一个类型为 add 、 参数为 1 的事件。
而 store.subscribe(render) 监听到了这个事件，并在响应之后调用 render 函数，从而刷新页面。

# react + redux
[react + redux Demo](https://codesandbox.io/s/redux-with-react-vfq19?file=/src/index.js)
和 react 结合的 redux 和上面几乎没有区别，只是页面的刷新从 js 的刷新变成了 react 的刷新。
还是点击加一的例子，步骤 1~5 同上。
其余代码如下
```
function App() {
  function add() {
    store.dispatch({ type: "add", payload: 1 });
  }
  return (
    <div>
      <div> {store.getState().number} </div>
      <button onClick={add}> 点我加一 </button>
    </div>
  );
}
function render() {
  ReactDOM.render(<App />, document.getElementById("root"));
}
render();
```

# react-redux
[react-redux Demo](https://codesandbox.io/s/react-redux-ko0b1?file=/src/App.js)
react-redux 是一个单独的依赖包，为了更便捷地在 react 中使用 redux 。
它把组件分成了 UI 组件和容器组件，UI 组件负责 UI 的呈现，而容器组件负责管理数据和逻辑。
我们知道，redux 是用来帮助我们管理数据的一个工具。
在 react-redux 中，如果一个组件既有 UI 又有业务逻辑，我们需要把它拆分为两个部分：外面是一个容器组件，里边包含一个 UI 组件。
依然是这个点击加一的例子。
1. 创建 reducer , 创建 store
2. 使用 Provider 将 store 传给子组件
    ```
    import { Provider } from "react-redux";
    ReactDOM.render(
        <Provider store={store}>
            <App />
        </Provider>,
        rootElement
    );
    ```
3. 在子组件 App 中接收 state . （创建一个 App.js 文件）
   ```
    function App(props) {
        return (
            <div>
                <div>{props.number}</div>
                <button onClick={props.add.bind(this)}>点我加一</button>
            </div>
        );
    }
   ```
   这个 App 就是 UI 组件，它没有自己的状态，直接接收 props 的 number 并展示在页面上。
   button 的 click 事件同样也是 props 传进来的。
4. 创建 state 转换成 props 的函数
    ```
    function mapStateToProps(state) {
        const { number } = state;
        return { number: number };
    }
    ```
    将 state.number 赋值给 props.number , 并返回一个新的对象。
5. 创建 dispatch 转换成 props 的函数
   ```
   const mapDispatchToProps = (dispatch) => {
        return {
            add: () => dispatch({ type: "add", payload: 1 })
            };
    };
    ```
    这里传入的是 dispatch ， 返回一个对象，对象里边有一个 add 函数，在 App 组件中使用 props.add 即可引用该函数。
    如果函数不复杂的话可以省去这一步，直接在 App 组件中使用 useDispatch 即可。
    ```
    const dispatch = useDispatch();
    ...
    <button onClick = {()=>{dispatch({ type: "add", payload: 1 })}}>...</button>
    ```
6. 连接 UI 和数据逻辑
    ```
    export default connect(mapStateToProps, mapDispatchToProps)(App);
    ```
    这里的语法不要写错`connection()()`
    第一个 connection() 的返回值是一个函数，然后 App 是返回的这个函数的参数，
    形如这样
    ```
    function add(a){
        a=a+1
        return function fn(b){
            return a+b
        }
    }
    console.log(add(1)(2))
    ```
react-redux 的好处在于在不污染全局变量的条件下，我们不需要将 store 层层传递，只需要在根节点上使用 Provider 就能够让所有的子组件获取 store 的值。

---------

以上，如果出现异步操作，action 发出之后，过一段时间再执行 reducer ，那么需要使用中间件 (middleware)。

---------

参考文献：
[阮一峰Redux 入门教程](http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_three_react-redux.html)
