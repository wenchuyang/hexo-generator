---
title: react入门使用
categories:
  - 计算机
  - 前端
tags:
  - react
date: 2021-04-03 16:17:19
---

react 的要点在于用 js 仿 html，保留了前端开发习惯的同时几乎把 html 彻底干掉了。
在写 js 语句的时候，用的就是 js 语法。
而在写 "html" 的时候，用的是 react 提供的语法。
这篇文章介绍了在 react 中如何使用 if/else 条件判断，for 循环，内联样式。

# if-else
[demo](https://codesandbox.io/s/if-4x9og?file=/src/App.js)
1. && `return flag && <div>hello</div>` 。 如果 flag 为真就返回 `<div>hello</div>`, 否则返回 false。
2. 冒号表达式 `return flag ? <div>hello</div>: <div>hi</div>` 。 如果 flag 为真就返回 `<div>hello</div>` 否则返回 `<div>hi</div>`。
3. 直接使用 if-else
   ```
    let content = ""
    if (flag) {
      content = <div>hello</div>
    } else {
      content = <div>hi</div>
    }
    return content
   ```

# for
[demo](https://codesandbox.io/s/for-sfznv?file=/src/App.js)
1. 创建一个数组，然后将 list 中的每一项裹上 div push 进去，再将这个数组放到页面上。
  ```
  // const infoList = ["item1", "item2", "item3"];
  const infoList = ["item1", "item2", "item3"];
  function App() {
    const content = [];
    for (let item of infoList) {
      content.push(<div>{item}</div>);
    }
    return <div className="App">{content}</div>;
  }

  // const objList = {"0": "a", "1": "b", "2": "c"}
  const content = []
  for (let key in objList) {
    let item = <div>{key}: {objList[key]}</div>
    content.push(item)
  }
  return <>{content}</>
  ```
2. 使用 Array.map 实现
   ```
   const content = infoList.map((value) => <div> {value} </div>);
   return <>{content}</>;
   ```
   当然，你也可以把它嵌入到下面
   ```
    return (
        <>
          {infoList.map((value, index) => (
            <div> {value} </div>
          ))}
        </>
      );
   ```

# 内联样式
```
function Box() {
  return <div style={{border: "1px solid red"}}>1111</div>;
}
```
这段代码翻译过来是
```
function Box() {
  return React.createElement("div", {
    style: {
      border: "1px solid red"
    }
  }, "1111");
}
```
这样我们就比较好理解语法了。
jsx 中 `<div>` 内如果我们没有加 `{}` 会默认认为是字符串，而加了大括号的话会以变量来编译。在 style 属性里添加 border 为 1px 。外面的大括号表明这是一个变量，里面的大括号表明这个变量是一个对象。
因为它实际是一个对象，所以如果有多个样式的话应该使用逗号分隔 `border: "1px", borderRadius: "5px"`。
因为 js 不支持 `-` 作为变量名，所以原本的 `border-radius` 变成了 `borderRadius`.
注意样式值需要用引号包裹，因为它应该是一个字符串。

--------

总的来说，用 js 的思想去考虑应该要怎么写。
