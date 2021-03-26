---
title: react开发环境搭建
categories:
  - 计算机
  - 前端
  - react
tags: [react, create-react-app, babel]
date: 2021-03-25 10:36:26
---


# codesandbox
[codesandbox](https://codesandbox.io/s/new) 可以帮助你快速开始写你的 react 项目。你可以在这里直接创建一个 react 项目。
新建 sandbox 的时候选择 React 模板，需要依赖的时候在文件下方的 Dependencies 里边添加依赖即可。
除此之外，它还支持链接到你的 github ， 你可以将这里的项目直接 push 到你的 github 仓库中。
点击右侧的猫，输入项目名，然后点击 Create new respository on Github 即可。当然同理，你也可以将你的 github project 导入进来。
使用流畅，不过不支持自定义 webpack 配置，需要的可以点击 topbar 上的 File ，export 到本地进行开发。

# 原始的 react
在你的任意项目中，或者是在 [jsbin](https://jsbin.com/?html,output) 等在线编辑器中，引入 react 和 react-dom 包，即可进行开发。
1. 引入资源文件。在 [BootCDN](https://www.bootcdn.cn/) 中搜索 react ， 选择 `./umd/react.development.min.js` 开发包，引入到项目中。 react-dom 同理。
2. 开始写。你可以参考并改写[这里](https://jsbin.com/jaratof/3/edit?html,js,output)的代码来尝试 react 。
  ```
  function App() {
    return React.createElement("button", {
      onClick: ()=>alert(1111)
    }, "Click")
  }
  ReactDOM.render(React.createElement(App), document.getElementById("xxx"))
  ```
  或者
  ```
  ReactDOM.render(React.createElement("button", {
      onClick: ()=>alert(1111)
    }, "Click"), document.getElementById("xxx"))
  ```
  以上代码效果相同，都是创建一个 button 并挂到 id 为 "xxx" 的页面元素下。

# 使用 jsx 的 react

jsx 省去了 `React.createElement()` , 直接使用类似于 html 的语法来写代码，而配置其他的工具来将代码解析成 `React.createElement()` 。
使用 [babel 在线翻译](https://www.babeljs.cn/repl) 可以实时查看 jsx 翻译成 js 之后的代码。

## 在线开发
在 jsbin 中，点击 javascript 可以切换到 jsx 语法，你可以在此书写你的代码。[这是使用 jsx 语法改写的简单的例子](https://jsbin.com/jaratof/5/edit?html,js,output)，和上一步中的代码功能一致。
```
function App() {
  return <button onClick={()=>alert(1111)}>Click Me</button>
}
ReactDOM.render(<App/>, document.getElementById("xxx"))
```

## 本地开发
本地开发和使用 jsbin 不同的是你需要自己配置 babel 来解析 JSX 代码。
如果碰到问题请参考[Babel官方文档](https://babeljs.io/docs/en/babel-cli)
1. 创建一个文件夹，用来存放你的环境和源码。
2. `npm init` 创建一个合法的 package.json 文件
3. `npm install --save-dev @babel/core @babel/cli @babel/preset-env`
    `npm install --save @babel/polyfill`执行这两行命令安装 babel 包。
4. 创建一个 config 文件 `babel.config.json`, 代码如下：
   ```
   {
        "presets": [
            [
                "@babel/env",
                {
                    "targets": {
                    "edge": "17",
                    "firefox": "60",
                    "chrome": "67",
                    "safari": "11.1"
                    },
                    "useBuiltIns": "usage",
                    "corejs": "3.6.5"
                }
            ]
        ]
    }
   ```
5. `npm install --save-dev @babel/plugin-transform-react-jsx` 安装 plugin-transform-react-jsx ，这样才能识别 jsx 语法。
6. 修改 babel.config.json 文件，添加
   ```
   "plugins": [
        [
        "@babel/plugin-transform-react-jsx"
        ]
    ]
   ```
   与 presets 平级。
7. `npx babel main.jsx --out-file main-compiled.js` 将 main.jsx 文件编译成 main-compiled.js 文件，然后在你的 index.html 中引入 main-compiled.js 文件即可。当然，不要忘了在你的 index.html 文件中引入 react 和 react-dom .
8. `npx babel src/js --watch --out-dir dist/js` 如果你需要实时更新的话，这个命令会帮你开一个进程，实时关注你的 src/js 文件夹内的更新，一旦有了改动，会输出到 dist/js 文件夹中。

# create-react-app
借助 [create-react-app](https://github.com/facebook/create-react-app) 来构建一个本地项目。
1. 创建一个 demo 文件夹，然后进入 demo，执行 `yarn add create-react-app` 或者 `npm install create-react-app`。或者全局安装。
2. `npx create-react-app my-app` 或者 `yarn create react-app my-app` 创建一个名字是 my-app 的项目。
3. 此时你已经得到一个完整的 react 项目了，`cd my-app` 然后执行`npm start` 或者 `yarn start` 可以开启一个本地服务，预览你的项目。
4. 执行 `npm run build` 或者 `yarn build` 之后可以得到一个 build 文件夹。在你的 `package.json` 文件中添加 `"homepage": "https://yourusername.github.io/my-app/build"` 之后再进行 build ，然后将你的代码上传至你的 GitHub ，配置 github page 之后，可以在线预览你的项目。

附（各文件作用）：
```
package.json 依赖和脚本等东西
yarn.lock 目前安装的东西，真实使用的东西。一个记录。
src 所有的源码
    index.js 入口文件
    App.js 业务逻辑
public 公开的文件，一般是常年不更新的代码
build 构建之后的代码
```
