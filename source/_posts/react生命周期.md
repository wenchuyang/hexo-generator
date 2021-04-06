---
title: react生命周期
categories:
  - 计算机
  - 前端
tags:
  - react
  - 生命周期
date: 2021-03-30 21:54:20
---

# JS 的生命周期有哪些
暂且抛开 react ，单说生命周期。一个元素，从出生到死亡的全过程。
1. 创建元素
2. 填充内容
3. 挂载到页面
4. 更新内容
5. 销毁元素
[演示 Demo](https://codesandbox.io/s/js-lifecycle-vu9m7?file=/src/index.js)

# react 生命周期有哪些
[官网文档组件的生命周期](https://zh-hans.reactjs.org/docs/react-component.html#the-component-lifecycle)
1. 挂载相关
  *[constructor()](#constructor-NaN)
  [static getDerivedStateFromProps()](#static-getDerivedStateFromProps-props-state)
  *[render()](#render)
  *[componentDidMount()](#componentDidMount)
  _componentWillMount() 即将过时_
2. 更新相关
  static getDerivedStateFromProps()
  *[shouldComponentUpdate()](#shouldComponentUpdate)
  render()
  [getSnapshotBeforeUpdate()](#getSnapshotBeforeUpdate-prevProps-prevState)
  *[componentDidUpdate()](#componentDidUpdate-prevProps-prevState-snapshot)
  _componentWillUpdate() 即将过时_
  _componentWillReceiveProps() 即将过时_
3. 卸载相关
  *[componentWillUnmount()](#componentWillUnmount)
4. 错误处理（抛出错误的时候会调用的方法）
  [static getDerivedStateFromError()](#static-getDerivedStateFromError-error-amp-componentDidCatch-error-errorInfo)
  [componentDidCatch()](#static-getDerivedStateFromError-error-amp-componentDidCatch-error-errorInfo)

[生命周期示例](https://codesandbox.io/s/life-cycle-nvmcg?file=/src/App.js)
[生命周期图示](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)，点击 Show less common lifecycles 可看见所有的生命周期。
正常情况下当你创建了一个组件，会先执行 constructor 方法构建组件，然后是 getDerivedStateFromProps 来从 props 中获取新的 state 值（仅在某些特殊时候需要用），然后是 render 进行组件的渲染，最后挂载到页面之后会执行 componentDidMount 。
如果你需要更新一个页面，改变自己的 state 之后，会先执行 getDerivedStateFromProps ，然后执行 shouldComponentUpdate ，如果这个函数返回的是 false ，那么就不继续更新页面（但是此时的 state 值已经改了）。
如果确定要继续更新页面的话，会继续执行 render 进行渲染。getSnapshotBeforeUpdate 在渲染出来的新东西挂载到页面之前获取之前的状态，props 和 state，这个函数的返回值会作为 componentDidUpdate 的第三个参数。 componentDidUpdate 在组件更新之后执行。
如果子组件出现了错误，那么会先执行父组件的 getDerivedStateFromError 从错误信息获取新的 state 值，然后卸载子组件，在卸载子组件之前执行子组件的 componentWillUnmount 方法。然后执行父组件的 componentDidCatch 捕获错误信息写入日志。
如果子组件是正常卸载的话，只会执行子组件的 componentWillUnmount 。

# 生命周期方法使用

## constructor()
常用场景：
1. init state
2. 事件处理函数（注意使用箭头函数或者 bind 来避免 this 潜在的问题）

## render()
1. 返回一个 react 元素 `<div><h1>Hi</h1></div>`
2. 返回多个 react 元素 
   ```
   <React.Fragment>
      <h1>Hi</h1>
      <h1>Hello</h1>
      <h1>Valemagalas</h1>
    </React.Fragment>
    ```
    或者省去 React.Fragment , 形如
    ```
    <>
      <h1>Hi</h1>
      <h1>Hello</h1>
      <h1>Valemagalas</h1>
    </>
    ```
3. 返回一个挂载在某个元素上的元素，常用于创建对话框。
  ```
  return ReactDOM.createPortal(
    this.props.children,
    domNode
  );
  ```
  假如这是一个对话框，组件名为 Dialog , 我们可以把 domNode 设置为根元素，然后在这里面写对话框的具体内容 `<Dialog>...</Dialog>`
4. 返回 Boolean ，常用于 `return flag && <Child />` ，flag 为 boolean ，如果 flag 为 true ，则渲染 Child 组件，否则什么都不渲染。

js 有 6 种原始数据类型和三种复杂数据类型: Undefined(undefined), Boolean(false), Number(123), String(""), BigInt(BigInt("9")/9n), Symbol(Symbol()), Null(null), Object({}), Function(()=>{})
render 不能返回 undefined, Object, 可以返回 Boolean, Number, String, BigInt, Symbol, Null, Function。
其中返回 String 或者 Number，比如 `hello` 。它会把它直接渲染到页面。
BigInt 和 Symbol, Boolean 不会报错。Function 会给出一个警告。
允许 `return null` 但是不允许 `return undefined`


## componentDidMount()
已经 render ，挂到了 DOM 树上，但是还没有显示到屏幕。
常用场景：
1. 向后台请求数据
2. 设置 subscription（添加事件订阅），注意要在 componentWillUnmount() 里边 unsubsribe。

## componentDidUpdate(prevProps, prevState, snapshot)
在触发更新之后调用，初始化的时候不会调用。
如果组件实现了 getSnapshotBeforeUpdate() 生命周期，它的返回值就是这个的第三个参数。
如果 shouldComponentUpdate() 的返回值为 false，则不会调用 componentDidUpdate()

## componentWillUnmount()
组件销毁之前调用

## shouldComponentUpdate()
首次渲染或者使用 forceUpdate() 时不调用此方法，返回 false 时会阻止组件更新。

## forceUpdate() & setState() 
这两个是我们主动调用，其它都是 react 自动调用。
更新一般发生在 state 或者 props 发生改变的时候，但如果你使用了其他数据，可以用 forceUpdate 来强制更新。
forceUpdate 不会触发 shouldComponentUpdate 。
```
class Box1 extends React.Component {
  constructor() {
    super();
    this.name = 2333
  }
  handleClick = ()=>{
    this.name = Math.random()
    this.forceUpdate()
  }
  render() {
    console.log("render");
    return (
      <div> hello, {this.name}
        <button onClick={this.handleClick}> Click me </button>
        </div>
    )
  } 
}
```

## static getDerivedStateFromProps(props, state)
会在调用 render 之前调用，返回一个对象来更新 state，如果返回 null 则不更新任何内容。[使用方法](https://codesandbox.io/s/getderivedstatefromprops-8zhti?file=/src/App.js)
每次渲染前都会触发这个方法，不论是 props 更新了还是 state 更新了。

## getSnapshotBeforeUpdate(prevProps, prevState)
在 update 之前获取一些信息，返回值会传给 componentDidUpdate()

## static getDerivedStateFromError(error) & componentDidCatch(error, errorInfo)
[Error boundaries demo](https://codesandbox.io/s/error-boundaries-g35gc?file=/src/App.js)
错误边界，捕获子组件的错误。如果子组件发生了错误，那么显示降级 UI 。这两个用来做这件事情。
getDerivedStateFromError 将抛出的错误作为参数，返回新的 state 值。在渲染阶段调用，不允许有副作用（不确定的输出结果）。可以用来设置 state 值，从而渲染降级 UI。
componentDidCatch 在后代组件抛出错误时调用，允许有副作用。可以用来写 error log。

# 使用总结
1. constructor: 数据初始化（state 初始化）、事件监听。
  其中数据更新会放在点击事件里边，需要注意的是 setState 会触发组件的更新，所以不要在组件更新的钩子里写 setState 。
2. componentDidMount: 数据请求、事件订阅
3. shouldComponentUpdate: 手动阻止更新

遇到什么操作该放到哪里的问题时，反向想一想它不能放在哪。比如说 ajax 请求，如果我需要获取数据，那么按说挂载的几个生命周期都可以，constructor, getDerivedStateFromProps, render, componentDidMount, 但是官网说 constructor 中要避免引入副作用， getDerivedStateFromProps 主要用来从 props 获取 state， render 在每次更新的时候都会调用，所以只能放在 componentDidMount 里边了。


