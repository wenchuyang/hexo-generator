---
title: ReactRouter使用指南
categories:
  - 计算机
  - 前端
tags:
  - react
  - react-router
date: 2021-03-29 18:32:56
---
router 是路由，在互联网上，把信息从源地址传输到目的地址的活动。
React Router 是一个基于 React 的路由库，帮助我们在解析路由的同时保持页面与 URL 之间的同步。

# React Router 的使用
[一个简单的 tab 页](https://codesandbox.io/s/react-router-wmxnl?file=/src/App.js)
1. 引入 react-router-dom 库，因为是在浏览器上的应用，不是 native ，所以引入的是 -dom 库
2. 在代码中添加路由
  ```
  import { BrowserRouter as Router, Link, Switch, Route } from "react-router-dom";
  <Router>
    <div>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/users">Users</Link>
      </nav>
      <Switch>
        <Route path="/about">about</Route>
        <Route path="/users">users</Route>
        <Route path="/">home</Route>
      </Switch>
    </div>
  </Router>
  ```
  实现的效果是点击 nav 中的按钮之后下面的 switch 中会自动显示相应的区域，并且 URL 也会自动添加相应的后缀。而且在修改了 URL 之后，switch 中也会自动变化。

这就是 React Router 帮我们实现的。

# 自己实现 React Router 效果
[Demo 源码](https://codesandbox.io/s/tab-qiehuan-8tpdq?file=/src/App.js)
1. 创建一个组件，有 nav 区域和 content 区域。
2. 添加 this.state , 然后给 nav 里面的元素添加点击事件来修改 state 的值。
3. 给 content 区域添加条件渲染，至此已经完成了一个 tab 切换效果。
   ```
   render() {
    let button = "";
    switch (this.state.display) {
      case "users":
        button = <div>users</div>;
        break;
      case "about":
        button = <div>about</div>;
        break;
      default:
        button = <div>home</div>;
    }
    return (
      <div className="App">
        ...
        <div className="content">{button}</div>
      </div>
    );
   }
   ```
4. 现在，在切换的时候修改 url ，`window.location.hash = "about"`
5. 通过 `window.location.hash` 来初始化 this.state.display 的值，从而实现 content 根据 url 的不同而修改。

这里使用 window.location.hash 来修改 url ，实际上修改路由还可以用 window.location.pathname 和 window.history.pushState() ,要注意他们的区别：
1. `window.location.hash = ` the best way, 不会刷新页面。
   ```
   window.location.hash = "home"
   console.log(window.location.hash)  // "#home"
   ```
2. `window.location.pathname = `，修改 pathname 的话，相当于改了路径，浏览器会以为你要跳转页面，于是自动刷新。
   ```
   window.location.pathname = "home"
   console.log(window.location.pathname) // "/home"
   ```
3. `window.history.pushState()`, 同样不刷新页面，但是需要后端将 default 路径都指向首页，不然可能会报这个页面不存在。
   ```
   window.history.pushState(null, "", "home")
   console.log(window.location.pathname) // "/home"
   ```

以上是不使用 React Router 实现 tab 切换的全过程，React Router 帮我们简化了这个过程。


   