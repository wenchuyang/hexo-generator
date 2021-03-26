---
title: React
tags: []
date: 2021-02-26 18:17:32
categories:
- 计算机
- 前端
---

1. 博客的整理


# redux
1. redux without react
   redux.createStore(counter)
   function counter(state(上一次 store 的状态), action(你即将进行的操作))
   - dispatch 一个 action
   - 根据操作生成新的 state ， 触发一个事件
   - 接收到事件，重新 render
2. redux with react
   create react project, 分不同的文件
   npm i create-react-app
   为了不一层一层往下调父元素的 fn 。每一个父元素都把 store 传给子元素，让子元素直接拿到 store ， 然后 dispatch 。 但是这个时候需要一层一层往下传。
3. React-Redux   ==> 后边 React 内置了 hooks 代替 redux
   npm install react-redux
   connect()(App) ----? connect return 了一个函数，这个函数再以 App 作为参数。
   Provider 传递 state ，App 被包裹在 Provider 里边， Provider 会把 state 传递给 App 。 使用 connect 来读取 App 。 不需要 dispatch ， 它会帮你 dispatch 。


 * redux 基本思路：
 * 把所有的变量放在一个地方
 * 如果有元素需要修改这个变量，那么提交一个修改变量的申请
 * 管家会监听并处理这个申请，然后重新 render 页面
 * 
 * redux 具体
 * 把所有的变量和处理函数放在 store 里，变量用 state 存储，处理函数用 action 存储。
 * 外部需要获取变量的话用 store.getState() 拿到 state 的值。
 * action 接收一个参数对象 {type: "xxx", payload: 1} , 其中 type 用来区分不同的 action ， payload 是 action 的参数。
 * var store = Redux.createStore(reducer) 来生成 store 。 其中 reducer 是一个函数，接收旧的 state ， 返回新的 state ， 并且负责进行 state 的初始化和 action 的分类处理
 * store.dispatch({ type: "add", payload: 1 }) 触发一个类型为 "add" 的 action ， 并且传入参数 1 。在需要调用的时候调用。
 * store.subscribe(render) 监听 action ，一旦 action 被 dispatch 了，就执行 render 函数。 render 函数负责刷新页面。

react + redux
yarn add create-react-app
npx create-react-app app
cd app
yarn start
yarn add redux
yarn add react-redux
使用 connect 连接 state、dispatch 和 组件

# Context
分享全局数据，可以不需要使用 props 传递。
被多个组件使用的时候才需要用到 context ， 如果只是为了避免一些属性被多层级地传递，那么可以使用 component composition 而不是 context。

1. 某个变量在各个函数之间的传递，如果很多个函数都需要这个变量。全局变量又不好用，如果在任何地方都能访问这个变量的话，可能会在某个地方被修改。所以设置局部的全局变量（只在这一块能用，并且在这一块它是全局变量）**_闭包_**。这个就是 context 。
2. Provider 提供值， Consumer 获取值。写法。
3. 变量的修改。通过 context 把修改的东西传过来。

和 Redux 的区别？
功能差不多。

创建一个 换肤 的应用。
创建 context
把东西包到 provider 里边
声明变量，传递变量
声明组件

# Hooks
在函数组件里使用 state 和其他的东西
16.7 开始支持。看下 API 。
npm info react versions / yarn info react versions 列出 react 所有的版本号
函数组件开始可以有状态。
const [count, setCount] = useState(0)
setCount(count++)

useContext 代替 redux

state 里边有复杂的结构，并且对数组里边增加修改和删除。
Hooks 只能在函数组件里边用。

useEffect
副作用：函数的行为依赖于外部世界。不知道哪来的东西，即为副作用。这个外界的东西，可能会被修改。比如依赖了 console.log 。纯函数指的是没有副作用的函数，比如 return a+b
把副作用函数全都写在 useEffect 里边。

hooks API 全看一遍 ，CRM。

# React Router
react 搞清楚， router 搞清楚。
路由：在互联网上，把信息从源地址传输到目的地址的活动。

webpack ？ide ？
let 和 const 区别？什么样叫做常量？

登录注册的切换。使用变量控制 Box1 和 Box2 的显示，然后用 setN 控制这个变量。
如果我想让一些人直接进入注册，另一些人直接进入登录？
改 URL window.location.hash = 'login' 。 不同的界面对应不同的 URL。
设置初始值，根据 hash 的值来设置 ui 的初始值。然后在点击切换的时候， setUi 的同时修改 window.location.hash 的值。
注意： 如果你修改 pathname 的话，相当于改了路径，浏览器会以为你要跳转页面，于是自动刷新，但如果改的是 hash ， 浏览器不会刷新。
改 pathname 的话会刷新页面，而且后端不一定支持这个页面，于是会返回 404 。
window.history.pushState(null, "", "/login") 同样不刷新，但是有些地方可能会报这个页面不存在。使用 pathname 读取。
总结：
1. window.location.hash = 可行
2. window.location.pathname = 会刷新页面，不可行
3. window.history.pushState() 后端如果将所有路径都指向了首页就可以这样做，否则不行。 codesandbox 帮我们做了这个。

加一个 welcome 页面呢？
如果 path 很多个呢？ 
如果路由不确定呢（比如每个用户有一个特定的页面）？ 正则？ react router 的库。

保持 UI 和 URL 同步。

react-router-dom 给浏览器用的。

原理 和 react 的优化。

使用原生 js 实现，使用 react 实现， 使用 react-router 实现，使用 vue 实现。


# react 生命周期
新创建的某个组件的生命周期。
JS 生命周期：
create (createElement) - 填充内容 - mount (appendChild) - update (update the element) - destory (把你占用的所有资源都移除掉，事件监听， div 本身， 等等)
JS 垃圾回收？

React 生命周期：
constructor - componentWillMount - render (填充/更新内容) - componentDidMount - componentWillUpdate - update is render - componentDidUpdate - componentWillUpmount
componentWillReceiveProps 父组件要给子组件传递新的信息。在 componentWillUpdate 之前。

页面常用操作：
1. 请求数据 ajax
2. 更新数据 setState
3. 事件监听 onClick
4. 初始化 state   

请求数据越早越好，写在哪里都可以，但是最好是 1. 创建的时候就请求。 2. 写在事件处理函数里边。
初始化 state ，如果你在 constructor 里边没有初始化 state 的话，会自动帮你把 state 置为 null 。 在使用它之前，在哪里初始化都可以，但是最好是在 constructor 里边。
更新 state ， 最好是在 onClick 方法里边。在 mount 之前不可以更新，在 render 里边不可以更新（不然 setState 之后又要 render ）。setState 会触发更新事件。所以将要更新、更新和已经更新的时候不可以 setState 。

使用 shouldComponentUpdate 可以手动阻止更新（return false），避免不必要的更新。

面试题：
9个 生命周期。
性能优化 shouldComponentUpdate
哪一步发 ajax 请求。componentDidMount。 constructor 里边不能 setState， componentWillMount 里边由于 Fiber 会有些问题。render 不能发请求。？？？把前面几个排除掉。
用户 setState 之后，会调用哪几个生命周期函数？4 个。 should will render did


# tips

1. how to get form data with js
2. how to get DOM element
3. 自定义钩子的使用


# 番茄项目
番茄工作法
主要文档看一遍。