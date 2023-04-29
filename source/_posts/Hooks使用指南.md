---
title: Hooks使用指南
categories:
    - 计算机
    - 前端
tags:
    - react
    - Hooks
date: 2021-04-02 12:08:51
---

# 16.8 新增特性

作用：

1. react 组件分为函数组件和类组件，Hooks 可以让我们在函数组件中使用一些类属性的东西。
2. 使用自定义 hook 可以复用状态逻辑

使用规则：

1. 只能在函数最外层调用 Hook，不要在循环、条件判断或者子函数中调用。确保每次都以同样的顺序调用。原因点[这里](https://github.com/z-memo/interview/issues/148)
2. 只能在 React 的函数组件和自定义 Hook 中调用 Hook

# 把 class 组件改造成 function 组件

class 组件和 function 组件有两大区别，一是有自己的 state ，二是有自己的生命周期。
而 Hooks 给我们提供了一些 API，可以让我们在 function 中使用 state 和生命周期。

## 在 function 组件中使用 state

[Demo](https://codesandbox.io/s/usestate-yvt0y?file=/src/App.js)
在函数组件中使用 useState 。

```
import {useState} from "react"
function Div() {
  const [a, setA] = useState(0)
  return <div>{a}</div>
}
```

这段代码在 Div 里面声明了一个变量 a 和一个 setA 的函数，如果我需要将 a 的值改为 1 ，那么可以直接 `setA(1)`.
其中声明 state 的代码 `const [a, setA] = useState(0)` 相当于

```
const xxx = useState(0) // 返回了一个有两个元素的数组
const a = xxx[0]
const setA = xxx[1]
```

如果我们需要多个元素的话，可以使用多次声明的方法，也可以借助对象来实现。

```
const [age, setAge] = useState(0)
const [name, setName] = useState("Cindy")
// name 获取 name 值
```

或者

```
const [info, setInfo] = useState({
  age: 0,
  name: "Cindy"
})
// info.name 获取 name 值
```

需要注意的是在 class 组件中，如果我们使用 `this.setState({name: "Wency"})` 不会覆盖掉 age 属性，但是在这里会直接覆盖掉。
因此我们如果需要覆盖而不是替换，则需要手动重新赋值。这里使用 ES6 的解构可以快速完成：

```
setInfo({
  ...info, // 把 info 这个对象里的每一项都列出来
  name: "Wency"
})
```

## 在 function 组件中使用 lifecycle

useEffect 几乎可以满足我们的需求。[使用 useEffect 模拟生命周期 demo](https://codesandbox.io/s/useeffect-xxhcn?file=/src/App.js)
useEffect 在组件每次渲染的时候都会调用，使用方法如下。

```
function MyComponent() {
  useEffect(function(){
    console.log("我被调用了")
  })
  return <div>111</div>
}
```

在 useEffect 内部返回一个函数的话，这个函数会在下一次更新的时候先运行，然后再运行 useEffect 内部的语句。
下面这段代码在函数组件第一次被挂到页面的时候会在控制台输出`1`。以后一旦组件更新了，会先执行 `console.log(0)` 再执行 `console.log(1)`

```
function MyComponent() {
  useEffect(function(){
    console.log(1)
    return ()=>{console.log(0)}
  })
  return <div>111</div>
}
```

如果需要只在组件被卸载的时候执行该语句，那么需要给 useEffect 传入第二个参数 `[]`。
useEffect 的第二个参数指定了哪些数据发生变化的时候执行这个 effect ，比如我想在 name 修改的时候执行 `console.log("I changed my name")` 而 age 发生修改的时候什么都不做，那么只需要传入 `[name]` 即可。

```
useEffect(()=>{
  console.log("I changed my name")
}, [name])
```

需要注意的是如果你的函数里面依赖了某些变量，那么这个变量也需要放在数组里。有一种情况需要特别注意：

```
useEffect(()=>{
  setAge(age=>(age+1))  //setAge(age + 1)  is wrong
}, [name])
```

绝对不要在 setAge 的同时在数组里面添加 age，不然会一直循环调用。

总结：

1. `useEffect(()=>{...})` 在每次渲染之后都会调用
2. `useEffect(()=>{...}, [count])` 在 count 改变的时候调用
3. `useEffect(()=>{...}, [])` 仅在组件挂载和卸载时执行
4. `useEffect(()=>(()=>{...}), [])` 仅在组件卸载时执行

在 class 组件中，如果你需要执行一个 subscribe，那么需要在 componentDidMount 里边 subscribe 一下，然后再在 componentWillUnmount 里边 unsubscribe
而在使用 useEffect 的情况下，你需要在函数里进行 subscribe, 然后再 return 一个函数执行 unsubscribe。形如

```
useEffect(()=>{
  xxx.subscribe()
  return ()=>{
    xxx.unsubscribe()
  }
})
```

好处是我们可以根据代码的用途来分离它们。并且每次组件更新的时候会自动 unsubscribe 前面的，然后重新 subscribe。（先清除上一个 effect，再执行这个 effect。）如果是在 class 组件中，则需要通过 componentDidUpdate 来做这件事。

抛开 lifecycle 不谈，所有有副作用的函数或表达式都应该在 useEffect 里面执行。
副作用（Side Effect）：函数或者表达式的行为依赖于外部世界

1. 函数或者表达式修改了它的 scope 之外的状态
2. 函数或表达式除了返回语句外还与外部世界或者他所调用的函数有明显的交互行为
   作用：产生了影响
   副作用：可能产生意料之外的影响
   比如，我要在一个函数里修改 DOM 元素，正常是没有问题的。但是我有可能找不到这个元素，一旦我找不到这个元素，那么就会有意料之外的报错。这就是副作用。
   再比如，我在函数内部调用了 `console.log(111)` ,正常调用这个函数应该会在控制台输出 `111` 。 但是这个 `console.log` 函数是别处来的，可能会被篡改，比如 `console.log = ()=>{alert(111)}` 。此时我的函数执行结果就会是 alert 一个 `111`。这同样是意料之外的结果。
   所以一个没有副作用的函数，应该是函数本身的行为不依赖外部世界的函数。数据获取、设置订阅以及手动修改 DOM 都属于副作用。

# useContext 获取 context 的值

在函数组件中使用 `const theme = useContext(ThemeContext)` 来获取 context 的值。
在类组件中使用`const theme = this.context` 来获取 context 的值，需要在 class 外面使用 `MyClass.contextType = SecretContext` 进行 context 的绑定。

# 自定义一个 Hook

[使用自定义 Hook 模拟 componentDidUpdate](https://codesandbox.io/s/zidingyihook-ui7ug?file=/src/App.js)

1. 函数名使用 use 开头，比如 `function useDidUpdate(){}`
2. 自定义 Hook 里面可以使用 react 内置的 Hooks
3. 用来提取可复用的状态逻辑。比如我如果在很多组件中都要用到 componentDidUpdate, 我不想把这个逻辑写很多次，所以就提取出来，封装成一个函数，在各个地方调用。或者我需要写入 localStorage, 封装一个写入的方法，只使用这个方法来调用。
