---
title: webpack
categories:
  - 计算机
  - 前端
tags: []
date: 2021-02-22 13:13:44
---


# 前言
1. webpack 是什么？它是一个开发工具。
2. 它有什么用？将你写的源码编译成浏览器能够看懂的代码。
3. 什么时候用？布置我们的开发环境的时候用。
   
或许这样的描述仍然不够清晰，那么不妨想象一下，如果没有 webpack ，会怎么样？
如果我们想要在一个项目中使用 sass ， 而 sass 不能被浏览器正确地解析，那么我们是不是需要一个 sass 解析的工具，从而能让我们开发的时候能够使用 sass ？ node-sass 就是用来做这件事情的。
ECMA 规范不断地发展，而用户多的时候总有那么些人用的是旧版本的浏览器，为了做兼容我们难道要一直使用旧的属性吗（ [Can I use](https://caniuse.com/) 可以搜索属性是否存在兼容性问题 ）？当然不是。Babel 就是用来解决这个问题的，它可以把我们的新代码重新编译成浏览器能看得懂的代码。
如你所见，我们使用新的技术总是会遇见很多的阻力，为了能赶上潮流，我们不得不借助一些工具。而前端的潮流变化太快，工具也变得更多了，那么如果有一种东西，能够集合所有的这些工具，会不会让我们的开发更方便一些？ webpack 就是这样做的。它集成了这些工具，让我们用起来更方便一些。
这里我先介绍一下不使用 webpack 的情况，再来讨论如果使用 webpack 的话应该是怎么样的。

# 一些工具的使用

## Sass
Sass 是 Ruby 社区发明的一种 css 语言，使用 Sass 可以使你的 css 写起来更加的简洁，但是浏览器不认识。
_`*.sass` 和 `*.scss` 都是 Sass 文件， 语法略微不同。`*.scss` 比 `*.sass` 多了括号。_
我们需要一个工具来把我们写的 Sass 翻译成浏览器可以识别的 css ，这就是 node-sass。
node-sass 给我们提供了一个 `-watch` 参数，可以实现自动化实时更新，免去了我们每次修改 Sass 文件之后都重新运行编译命令的麻烦。
```
npm install -g node-sass // 全局安装 node-sass
node-sass main.scss main.css  // 将 main.scss 翻译成 main.css
node-sass src/css -o dist/css  // 将 src/css 目录下的所有 Sass 文件 翻译成 css 并输出到 dist/css 目录下
node-sass src/css/main.scss dist/css/main.css -w  // 自动实时翻译
```
使用 `node-sass src/css -o dist/css -w` 可以实时监听 `src/css` 文件夹下所有的 scss 文件的改动并输出到 `dist/css` 文件夹下。

## Babel
node-sass 是翻译 sass 的工具，Babel 则是翻译 js 的工具。js 在不断地更新，ES5、ES6，TypeScript，等等，使用 Babel 可以将它们统一翻译为所有浏览器都可以接受的js。
[Babel官方文档](https://babeljs.io/docs/en/babel-cli)
1. `npm init` 创建一个合法的 package.json 文件
2. `npx babel main.js --out-file main-compiled.js` 使用 `npx babel` 代替 `./node_modules/.bin/babel`。`--out-file` 可以简写为 `-o`.
3. `npx babel src/js --watch --out-dir dist/js` `--out-dir` 可以简写为 `-d`, `--watch` 可以简写为 `-w`.

当你 install 的时候，添加 `-g` 参数代表全局安装，否则为项目内部安装。
项目安装的话会默认安装在 node_modules 里边，运行的时候需要用`./node_modules/.bin/babel main.js -o compiled.js`，而如果是全局安装的话，命令会包含在 path 里，在命令行直接输入 `babel main.js -o compiled.js` 即可。

## watch-cli
在上面的步骤里，我们了解了 node-sass 和 babel ，一个用来实时监听 scss 的改动，另一个用来实时监听 js 的改动。但这还不够，完整的代码里还需要 html。我们用 watch-cli 来监听 html 的改动，一旦它有了改动，就自动复制到 dist 目录下。

如此我们所有的代码应该都在 dist 目录下了。但是这样我们在写代码的时候同时开了三个进程，才能保证我们的代码能够正常地运行。
而如果你使用 webpack ，则只需要开一个 webpack 的进程即可。

# 回到 webpack
[webpack 官方网站](https://webpack.js.org/guides/getting-started/#basic-setup)

## webpack 的基础使用
这里我们创建一个 demo 来尝试做这件事情。项目目录如下：
```
webpackDemo
  src // 存放你要写的代码
    index.js
    style.scss
    test.html
  dist // 存放webpack生成的代码
```

### webpack 的安装与配置
1. webpack 的安装：`npm install --save-dev webpack`
2. 创建一个 webpack.config.js 文件，并在里边添加配置如下：
```
const path = require('path');
module.exports = {
    mode: "development",  // 官网上没写这一句，但是不加的话会报错。
    entry: 'src/index.js', // 你的 js 入口文件
    output: {
        filename: 'main.js',  // 编译之后输出的文件
        path: path.resolve(__dirname, 'dist/js'),  // 编译之后输出的文件夹
    },
};
```
3. 应用配置（不是很确定是否需要）`npx webpack --config webpack.config.js`
4. 运行 `npx webpack`，这个命令会执行代码的编译，在你修改代码完成之后执行这个命令，会将你的改动重新计算到 dist 文件夹里。

### 生成 html
1. 安装 html-webpack-plugin 插件（貌似还要安装 lodash）
```
npm install --save-dev html-webpack-plugin
npm install --save-dev lodash
```
2. 在 webpack.config.js 文件中添加如下配置：
```
module.exports = {
  plugins: [new HtmlWebpackPlugin({
    template: 'src/test.html' // 这里不加的话会给你默认创建一个 html，如果加了的话就会参考这个里边的内容创建 html
  })],
};
```
[更多功能看这里](https://github.com/jantimon/html-webpack-plugin#usage)

### 配置 sass
[官方文档看这里](https://webpack.js.org/loaders/sass-loader/)
1. 安装 sass-loader 
```
npm install sass-loader sass webpack --save-dev
npm install style-loader
npm install css-loader
```
2. 添加配置
```
module.exports = {
    module: {
        rules: [
        {
            test: /\.s[ac]ss$/i,
            use: [
            // Creates `style` nodes from JS strings
            "style-loader",
            // Translates CSS into CommonJS
            "css-loader",
            // Compiles Sass to CSS
            "sass-loader",
            ],
        },
        ],
    },
};
```
3. 在你的 index.js 文件中添加`import "./style.scss";`对这个 style 文件进行引入


### 配置 babel
[官方文档看这里](https://webpack.js.org/loaders/babel-loader/)
1. 安装 babel-loader `npm install -D babel-loader @babel/core @babel/preset-env webpack`
2. 添加配置
```
module: {
  rules: [
    {
      test: /\.m?js$/,
      exclude: /(node_modules|bower_components)/,
      use: {
        loader: 'babel-loader',
        options: {
          presets: ['@babel/preset-env']
        }
      }
    }
  ]
}
```
3. 如果你有其他的 js 文件，同样要在我们的入口文件 index.js 里边 import。
index.js
```
import x form './module-1'
x()
```
  module1.js
```
function fn() {console.log(1)}
export default fn
```

### 添加实时更新
在你的 webpack.config.js 文件中添加
```
module.exports = {
    watch: true,
}
```

# todo
打包工具的发展：grunt -> gulp -> webpack -> parcel? vite?

webpack 相关面试题, 你对webpack的理解？
1. 打包。比如使用 sass-loader 进行 sass 文件的翻译。
2. 模块化。index.js 作为入口文件，css 和 js 都在这里进行 import 。 而 webpack 翻译这个入口文件的时候，将引入的那些文件也翻译出来，将这些所有的文件都翻译成一个文件。