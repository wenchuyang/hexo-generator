---
title: react背景知识
categories:
  - 计算机
  - 前端
  - react
tags: [react]
date: 2021-03-25 15:17:53
---


网页三要素：HTML、CSS and JS，在 react 里应该是什么样子的呢？

# HTML
react 没有 HTML 。
这样说有点绝对，但是并不是完全不正确。你使用 create-react-app 创建一个 react 项目的时候，会发现在 public 文件夹下有一个 index.html 文件，里边有一个 id 为 root 的 div 。
但是你要知道，我们写的所有代码，都应该是在 src 文件夹下的。我们不需要去修改这个唯一的 html 文件，为什么？
抛开 react ，如果我们需要修改页面中的某个东西的时候，我们会怎么做？
获取元素，修改元素，然后再把元素放回去。
而 react 给了我们另一种可能——
创建一个元素，然后放到页面。需要更新的时候创建一个新的元素，然后替换掉之前的元素。省去了从页面获取元素的那一步。
还是那句话， react 没有 HTML 。
平时写在 html 文件里的东西， react 放到了 JavaScript 里边。 
react 使用 `React.createElement("div")` 来创建一个 `<div />` ，用 `React.render(React.createElement("div"), document.querySelector("#root"))` 将 `<div />` 挂到 id 为 root 的元素下。
在 react 中，html 唯一的作用就是提供一个节点，让 js 把生成的元素挂到这个节点上。
这样我们在需要修改 DOM 元素的时候，只需要用 js 直接修改，然后再重新 render ，将修改好的虚拟的 DOM 元素渲染到页面。省去了之前从页面获取 DOM 元素的步骤。

## 关于虚拟 DOM
DOM 全称 Document Object Model ，文档对象模型，将 html 页面元素与一个个对象相对应，方便我们使用 js 来操控 html 文档。比如 `var x = document.querySelector("#root")`,我们通过这段代码从 html 页面上获取了一个真实的 DOM 元素。变量 x 对应着页面上一个 id 为 root 的节点。
而我们使用`const x = React.createElement("div")`则创建了一个标签为 div 的虚拟 DOM ，它与页面上的 div 相对应。
总的来说，虚拟 DOM 是表示 DOM 节点的 js 对象，对应页面的真实的 DOM 元素。

# JavaScript
如果我想往页面加一个 button ，那么我应该这样写 `React.render(React.createElement("button"), document.querySelector("#root"))` ，如果这个 button 需要绑定事件，那么应该使用 
```
React.createElement("button", {
    onClick: ()=>{console.log("here are we")}
}, "Click me")
```
其中第一个参数是元素标签；第二个参数是一个对象，上边写了元素属性；第三个参数是元素的内容。这样我们创建好了一个虚拟 DOM。
感觉很麻烦。
所以有了 jsx 语法。
jsx 做了什么呢？它允许我们在 js 里写一段看上去像 html 的代码。
比如上述的 button 创建，你可以这样
```
<button onClick={()=>console.log("here are we")}>Click me</button>
```
其中`{}`里边是 js 的变量，如果不加的话会默认把它当做字符串。
需要注意的是在这里 onClick 是给 button 绑定一个事件，如果你直接写 `onClick = {console.log(1)}` ，意思是把 console.log(1) 当做一个事件绑定上去，于是在绑定事件的时候会执行这段代码。所以需要使用一个函数把它包裹起来，这样才是在点击按钮的时候执行这个匿名函数。
另，给函数加 class 的话用的是 `className="xxx"` 而不是 html 中的 `class="xxx"`。

# CSS
react 里的 css 有两种方案。

## 传统方案
传统的给元素加 class ，内容与样式分离。css 写在 css 文件里，然后 `import "./style.css"` 引入 css 。
这种方法的一个弊端在于我们需要特别注意 class 的互相影响问题。如果组件 A 有一个叫做 title 的类，而它引用的另一个组件 B 同样也有一个 title 类，B 组件会继承 A 组件设置的 title 的样式。
而为了干掉这个不确定的因素，部分人会选择以组件名为前缀的 class 名字，这样可以有效防止各组件之间的样式冲突。参考 BEM 命名规范。

## CSS in JS
因为上一个方案命名的繁琐为人诟病，而 css 没有办法做到自动区分两个重名的 class ，所以有人想，不要 css 了。
根据这个思路，很多人，想了很多不同的解决方案。
在 github 搜索 "css in js" 可以看到一个完整的列表——版本号、star 数、下载量、package 名，你可以根据需要自行选择好用的方案。
这里介绍三种
1. [styled-components](https://styled-components.com/docs/basics#installation) 在组件内部的 js 文件中写样式代码。
   ```
    // 样式
    const Title = styled.h1`
        font-size: 1.5em;
        text-align: center;
        color: palevioletred;
    `;
    // jsx
    <Title> hello </Title>
   ```
    组件之间是彼此分离的，而组件内部内容样式和行为杂糅在了一起，好处是不论这个组件写的如何，不会影响到其他的组件。
2. [react-css-module](https://github.com/gajus/react-css-modules) 配置略麻烦，解决方案比前面的那种纯净一点。
   ```
    import CSSModules from 'react-css-modules';
    import styles from './style.css';
    <h1 styleName='title'>hello</h1>
   ```
3. emotion 语法繁琐，有点像内联样式。
   ```
    /** @jsx jsx */
    import { jsx } from '@emotion/react'
    <h1 css={{ color: 'red' }}> hello </h1>
   ```

具体选择什么方案自然是仁者见仁，但是需要注意的是，如果你是打算要建一个 UI 库，那么请选择传统方案。
在 CSS in JS 方案中，虽然我们没有写类名，但是在运行的时候， js 会自动为我们生成一个类名，你可以在浏览器的检查元素里看见。
在你自己的应用中，这不是什么问题。但是如果是建 UI 库，别人不知道类名，就无法覆盖样式。

--------

以上， react 可能只有 js ....