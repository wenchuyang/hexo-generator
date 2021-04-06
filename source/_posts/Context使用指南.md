---
title: Context使用指南
tags: [react, context]
date: 2021-03-29 16:41:06
categories:
- 计算机
- 前端
---

# Context 作用
Context 用来共享全局数据，避免了使用 props 层层传递。
在很多个组件都需要用到同一个变量的时候，可以使用 Context 来设置一个局部的全局变量（只在这一块能用，并且在这一块它是全局变量）。
当前认证的用户、主题、首选语言等都可以通过 Context 来传递。

# Context 简单使用
[demo](https://codesandbox.io/s/context-s5z6u?file=/src/App.js)
Context 是 react 提供的，所以在 react 应用中不需要引入额外的包。
使用步骤：
1. 创建并导出一个 context (注意 import 不要引入错了)
   ```
    import React from "react";
    export const SecretContext = React.createContext({
        secret: "初始化"
    });
    ```
2. Provider 提供数据
    ```
    import { SecretContext } from "./context.js";
    <SecretContext.Provider value={{ secret: "this is secret" }}>
        <App />
        <App2 />
    </SecretContext.Provider>
    ```
    Provider 中 value 提供的值会在后面子组件中读取值的时候读取到。
    这里 value 传的值是一个对象 `{secret: "this is secret"}`，对象里边可以包含修改这个变量的方法等。
3. Consumer 获取数据
    ```
    import { SecretContext } from "./context.js";
    <SecretContext.Consumer>
      {({ secret }) => <div>我知道秘密: {secret}</div>}
    </SecretContext.Consumer>
    ```
    Consumer 获取数据的时候，会从自己往上找，找到了这个 Context 的 Provider 的时候就读取它的值。如果没有 Provider 的话就读取第一步创建 Context 时候的初始值。
    Consumer 里面是一个函数，函数传入的值是 Provider 提供的值，return 一个 div。
    这个函数展开应该是
    ```
    (value) => {
        secret = value.secret // {secret} = value
        return <div>我知道秘密: {secret}</div>
    }
    ```

除了上面的 Consumer ，对于 class 组件，使用 contextType 也可以获取 context 的值。
```
class MyClass extends React.Component {
  render() {
    let {secret} = this.context;
    return (<div>我知道秘密: {secret}</div>)
  }
}
MyClass.contextType = SecretContext;
```
对于函数组件，使用 useContext 可以获取 context 的值
```
function MyComponent() {
  let {secret} = useContext(SecretContext)
  return (<div>我知道秘密: {secret}</div>)
}
```

# 修改 context 的值
[demo](https://codesandbox.io/s/dongtaicontext-ll0rf?file=/src/App.js)
我们在父组件中通过 context 传递了一个变量给子组件，如果想要在子组件中修改这个变量，那么可以直接通过 props 给子组件传递一个修改变量的函数，子组件直接调用这个函数即可。
但如果层级太深，或者这个函数在很多地方都有用到，显然这个代码就不那么美观了。我们可以直接将修改变量的函数放在 context 里边，然后在需要的组件中获取这个函数。
所以，使用 context 传递全局变量，并且这个变量需要修改的话，可以采用以上两种方法。下面分别来介绍一下使用细节。

## 使用 props 给子组件传递函数修改
这个方法可以单纯的看做是父子组件通信，因为不论你是否撇开 context ，结论都是一样的。
props 是父组件传递给子组件的一些变量，在函数组件中，接收一个参数 props 来获取；而在类组件中，使用 this.props 拿到它。
在子组件中，获取父组件传过来的函数 changeTheme ， 并绑定到按钮的点击事件上。
```
function Toolbar(props) {
  return (
    <button onClick={props.changeTheme}>{props.children}</button>
  );
}
```
在父组件中，调用子组件并传值。
```
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      theme: themes.light
    };
    this.setLightTheme = () => {
      this.setState({
        theme: themes.light
      });
    };
  }
  render() {
    return (
      <div>
          <Toolbar changeTheme={this.setLightTheme}>光明</Toolbar>   
      </div>
    );
  }
}
```

如果你知道子组件是一个 button ，你甚至可以直接在 Toolbar 上绑定 onClick 事件，然后子组件直接获取所有的 props 从而拿到点击事件。像这样
```
// 父组件
<Toolbar onClick={this.setLightTheme}>光明</Toolbar>
// 子组件
<button {...props} />
```

By the way, 其中 props.children 可以获取子节点，因此你可以通过这个直接将孙子组件放到父组件上。比如
```
<div>
    <Toolbar>
        <GrandSon onClick={this.xxx} />
    </Toolbar>
</div>
```
渲染出来就是
```
<div>
    <button>
        <GrandSon onClick={this.xxx} />
    </button>
</div>
```

## 使用 Provider 给 Consumer 传递函数修改
通过 context 的 provider value 来传递变量的同时传递一个改变这个变量的函数。
首先创建一个父组件，组件的 state 为 theme 和 setTheme, 然后在 Provider 里把 this.state 传过去。
```
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      theme: themes.light,
      setTheme: () => {
        this.setState((state) => ({
          theme: state.theme === themes.dark ? themes.light : themes.dark
        }));
      }
    };
  }
  render() {
    return (
        <ThemeContext.Provider value={this.state}>
          <SetButton>set theme</SetButton>
        </ThemeContext.Provider>
    );
  }
}
```
子组件在获取 context 的时候拿到 setTheme 的值，然后将 setTheme 事件绑定到 button 点击事件上。
```
class SetButton extends React.Component {
  render() {
    let props = this.props;
    let { setTheme } = this.context;
    return <button onClick={setTheme}>{props.children}</button>;
  }
}
SetButton.contextType = ThemeContext;
```
