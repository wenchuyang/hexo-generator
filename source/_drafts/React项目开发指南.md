---
title: React 项目开发指南
categories:
  - 计算机
  - 前端
tags:
  - react
  - 环境配置
date: 2022-12-14 21:42:09
---

# 最终目标

本文最终的目的是用 React + TypeScript + SCSS + CSS Module + ESlint 搭建一个能正常运行的项目。
当前使用的 React 版本是 18.2.0。

# 创建一个 React 项目

这次使用的是 React 提供的 create-react-app :

```
npm install create-react-app -g // 全局安装 create-react-app，或者你也可以创建一个文件夹，在这个文件夹内安装，之后所有的 react 项目都在这里面创建
npx create-react-app my-app // 在当前目录下创建一个名为 my-app 的 react 项目
cd my-app // 进入到 my-app 文件夹
npm start // 运行程序
```

不出意外的话经过以上几步你已经有了一个可以正常运行的 react 项目了。你可以删掉一些你不需要的文件，再在 src 下整理出你的文件架构，一般在 src 下会有以下文件夹类型，可以根据不同项目选择性创建：

- `assets` ，储存一些图片资源
- `components` ，组件，包括公共组件和业务组件
- `constants` ，常量
- `pages` ，所有的页面
- `states` ，全局的一些 state
- `utils` ，工具函数等
- `types` ，数据类型，一般用作接口数据类型定义
- `apis` ，数据接口定义，写上请求名等东西，return 一个 promise
- `services` ，处理页面复杂的业务逻辑
- `hooks` ，公共的 hooks

# TypeScript + SCSS + CSS Module

因为 react 把这三者以及一些 loader 都集成进来了，所以理论上不需要做什么额外的配置，你可以新建一个 Home.tsx 文件和一个 Home.module.scss 文件跑一下项目看有没有什么问题。所以这个部分我着重列一下我遇到的问题以及我的解决方案。

## 无法引入 tsx 类型的组件

首先你要明确的是你的引用路径是正确的，其次，你在 tsx 文件中引入另一个 tsx 文件，是不需要加文件后缀的，即：如果你要引入 `pages/Home.tsx` ，直接 `import Home from './pages/Home'` 即可。
我在引入 tsx 文件之后执行程序遇到了报错，提示 cannot found module 'Home' ，自动引入的时候省略了 `.tsx` 后缀，但是在 index.js 中添加后缀能正常跑，在 App.tsx 文件中添加后缀又不行。
最后发现将 tsx 文件改为 jsx 文件能正常运行，由此断定是 tsx 文件的编译问题。
[解决方案](https://segmentfault.com/a/1190000019075274)：

1. 安装 ts-loder 用于处理 tsx 文件：`yarn add ts-loader -D`。我的版本是：`"ts-loader": "^9.4.2"`
2. 在 `webpack.config.ts` 文件（项目中没有的话就在根目录下新建该文件）中添加 ts-loader 的相应配置：
   ```
    module.exports = {
        module: {
            rules: [
                {
                    test: /\.tsx?$/,
                    loader: "ts-loader"
                }
            ]
        }
    };
   ```
3. 执行 `yarn start` 之后你可能会遇到一个不能使用 jsx 语法的报错：`Cannot use JSX unless the '--jsx' flag is provided` ，去 tsconfig 做一下配置即可。
   1. 执行 `npx tsc --init` 生成 `tsconfig.json` 文件。
   2. 给 `tsconfig.json` 添加配置：
      ```
          {
              "compilerOptions": {
                  "jsx": "react"
              }
          }
      ```

## tsx 文件内引用 scss 文件错误

在我解决了上面的问题之后，编译正常了，但是我的代码中对 'Home.module.scss' 文件的引用却报错：`Cannot find module './Title.module.scss' or its corresponding type declarations.` ，错误的原因在于需要一个声明文件，于是我在 `src/declaration` 目录下新建了一个 `index.d.ts` 文件，内容如下：

```
declare module "*.scss" {
	const stylePreLoaderD: {readonly[className: string]: string} = { };
	export default stylePreLoaderD;
}
```

参考资料见[这里](https://blog.csdn.net/qq_24185803/article/details/122720081)

## tsx 文件内引用 svg 文件错误

在 `index.d.ts` 文件中添加 svg 文件声明：

```
declare module "*.svg" {
  const content: any;
  export default content;
}
```

参考资料见[这里](https://webpack.js.org/guides/typescript/#importing-other-assets)

## 其他问题和解决方案

- `import React from "react"` 报错，忘记是什么原因了，后来没有复现出来，但是修改为 `import * as React from "react"` 报错就好了。
- 控制台报错 `Cannot find module 'sass'`，查了下说是 node-sass 弃用了，改成了 dart-sass，执行一下 `npm install sass --save-dev` 重新安装就好了，我安装的 sass 版本：`"sass": "^1.56.2"`。具体在[这里](https://stackoverflow.com/questions/67161579/cannot-find-module-sass)。
- 报错 `Cannot find module 'react'/'react-dom'`，执行 `npm i @types/react -s` 和 `npm i @types/react-dom -s`，安装它们的类型定义文件并保存到 dependencies 里面。我安装的版本是 `"@types/react": "^18.0.26"` 和 `"@types/react-dom": "^18.0.9"`。具体见[这里](https://juejin.cn/post/6844903684422254606)

# ESlint 配置

## 安装插件

```
npm install --save-dev @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint typescript
```

我的版本：

```
"dependencies": {
    "eslint": "^8.29.0",
},
"devDependencies": {
    "@typescript-eslint/eslint-plugin": "^5.46.1",
    "@typescript-eslint/parser": "^5.46.1",
    "eslint-plugin-react": "^7.31.11",
    "typescript": "^4.9.4"
}
```

## 配置

命令行执行 `npx eslint --init` 会有一个简单的初始化，初始化过后 eslint 会自动为你生成一个 .eslintrc.js 文件，然后在 rules 里面按需配置，我的配置文件将在后面写明。

## 安装插件

我用的是 VSCode , 直接搜索 ESlint 下载安装即可。遇到的一个问题是与 prettier 格式化发生冲突，这里提供两种解决方法：

1. 禁用 prettier
2. 添加 `.prettierrc` 文件，把冲突的地方配置成与 ESlint 一致

## 保存时自动格式化

设置如下（经测试好像是上面那条生效的，但我还是两条都加上去了）：

```
"editor.formatOnSave": true,
"editor.codeActionsOnSave": {
    "source.fixAll": true
}
```

这是我的 VSCode 中其他 ESlint 相关配置：

```
"[javascript]": {
    "editor.defaultFormatter": "dbaeumer.vscode-eslint"
},
"[typescript]": {
    "editor.defaultFormatter": "dbaeumer.vscode-eslint"
},
"eslint.validate": [
    "javascript",
    "javascriptreact",
    "vue",
    "typescript",
    "typescriptreact",
    "*"
]
```

## ESlint 配置文件

这是我的[配置文件](https://github.com/wenchuyang/color-card/blob/master/.eslintrc.js)：

```
module.exports = {
    root: true,
    "env": {
        "browser": true,
        "es2021": true
    },
    "extends": [
        "eslint:recommended",
        "plugin:@typescript-eslint/recommended"
    ],
    "overrides": [
    ],
    parser: "@typescript-eslint/parser",
    "parserOptions": {
        "ecmaVersion": "latest",
        "sourceType": "module"
    },
    "plugins": [
        "@typescript-eslint"
    ],
    "rules": {
        /*
            下面这些rules是用来设置从插件来的规范代码的规则，使用必须去掉前缀eslint-plugin-
            主要有如下的设置规则，可以设置字符串也可以设置数字，两者效果一致
            "off" -> 0 关闭规则
            "warn" -> 1 开启警告规则
            "error" -> 2 开启错误规则
        */

        "space-before-function-paren": 1, // 函数定义时括号前面要不要有空格
        "eol-last": 0, // 文件以单一的换行符结束
        "no-extra-semi": 0, // 可以多余的分号
        "semi": 2, // 语句可以不需要分号结尾
        "eqeqeq": 1, // 必须使用全等
        "one-var": 0, // 连续声明
        "no-undef": 0, // 可以 有未定义的变量
        "max-len": ["error", { code: 120 }], // 单行最大长度
        "indent": ["error", 4], // 缩进
        "no-extra-boolean-cast": 1, // 不必要的bool转换
        "no-extra-parens": 1, // 非必要的括号
        "no-empty": 0, // 块语句中的内容不能为空
        "no-use-before-define": [1, "nofunc"], // 未定义前不能使用
        "complexity": [1, 10], // 循环复杂度
        "no-unused-vars": 1, // 不能有声明后未被使用的变量或参数
        // react
        // 'react/jsx-uses-react': 2,
        // 'react/jsx-uses-vars': 2,

        // 错误
        "comma-dangle": [2, "never"], // 对象字面量项尾不能有逗号
        "no-debugger": 0, // 禁止使用debugger
        "no-constant-condition": 0, // 禁止在条件中使用常量表达式 if(true) if(1)
        "no-dupe-args": 2, // 函数参数不能重复
        "no-dupe-keys": 2, // 在创建对象字面量时不允许键重复 {a:1,a:1}
        "no-duplicate-case": 2, // switch中的case标签不能重复
        "no-empty-character-class": 2, // 正则表达式中的[]内容不能为空
        "no-invalid-regexp": 2, // 禁止无效的正则表达式
        "no-func-assign": 2, // 禁止重复的函数声明
        "valid-typeof": 2, // 必须使用合法的typeof的值
        "no-unreachable": 1, // 不能有无法执行的代码
        "no-unexpected-multiline": 2, // 避免多行表达式
        "no-sparse-arrays": 1, // 禁止稀疏数组， [1,,2]
        "no-shadow-restricted-names": 2, // 严格模式中规定的限制标识符不能作为声明时的变量名使用
        "no-cond-assign": 1, // 禁止在条件表达式中使用赋值语句
        "no-native-reassign": 2, // 不能重写native对象

        // 代码风格
        "no-else-return": 1, // 如果if语句里面有return,后面不能跟else语句
        "no-multi-spaces": 1, // 不能用多余的空格
        "key-spacing": [
            1,
            {
                // 对象字面量中冒号的前后空格
                beforeColon: false,
                afterColon: true
            }
        ],
        "block-scoped-var": 1, // 块语句中使用var
        "consistent-return": 0, // return 后面是否允许省略
        "accessor-pairs": 2, // 在对象中使用getter/setter
        "dot-location": [2, "property"], // 对象访问符的位置，换行的时候在行首还是行尾
        "no-lone-blocks": 2, // 禁止不必要的嵌套块
        "no-labels": 2, // 禁止标签声明
        "no-extend-native": 2, // 禁止扩展native对象
        "no-floating-decimal": 0, // 禁止省略浮点数中的0 .5 3.
        "no-loop-func": 2, // 禁止在循环中使用函数（如果没有引用外部变量不形成闭包就可以）
        "no-new-func": 2, // 禁止使用new Function
        "no-self-compare": 0, // 不能比较自身
        "no-sequences": 0, // 禁止使用逗号运算符
        "no-throw-literal": 2, // 禁止抛出字面量错误 throw "error";
        "no-return-assign": [2, "always"], // return 语句中不能有赋值表达式
        "no-redeclare": [
            2,
            {
                // 禁止重复声明变量
                builtinGlobals: true
            }
        ],
        "no-unused-expressions": [
            2,
            {
                // 禁止无用的表达式
                allowShortCircuit: true,
                allowTernary: true
            }
        ],
        "no-useless-call": 2, // 禁止不必要的call和apply
        "no-useless-concat": 2,
        "no-void": 2, // 禁用void操作符
        "no-with": 2, // 禁用with
        "space-infix-ops": 2, // 中缀操作符周围要不要有空格
        "valid-jsdoc": [
            2,
            {
                // jsdoc规则
                requireParamDescription: true,
                requireReturnDescription: true
            }
        ],
        "no-warning-comments": [
            2,
            {
                // 不能有警告备注
                terms: ["todo", "fixme", "any other term"],
                location: "anywhere"
            }
        ],
        "curly": 1, // 必须使用 if(){} 中的{}
        "quotes": ["warn", "double"] // 必须使用双引号
    }
};
```

# TODO

1. package.json homepage 的设置
2. normorize.css 文件

# 参考资料

[TypeScript 中文手册-React 与 Webpack](https://typescript.bootcss.com/tutorials/react-&-webpack.html)
[typescript-eslint](https://typescript-eslint.io/getting-started/)
