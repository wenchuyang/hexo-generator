---
title: React父组件调用子组件方法
categories:
    - 计算机
    - 前端
tags:
    - react
date: 2023-03-11 16:36:21
---

# 父子皆为 function 组件

## useRef、forwardRef 和 useImperativeHandle 组合使用

使用 useRef 创建一个 ref 变量，然后赋值给子组件的 ref，这样可以在父组件中通过 ref.current 拿到子组件本身。理论上是这样，但是对于函数组件，因为他们没有实例，无法直接进行 ref 赋值。React 对此的解决方法是通过 forwardRef 进行转发，把外部调用的当前组件的 ref 转发到当前组件的 DOM 元素上。具体使用方法可以看[这里](https://beta.reactjs.org/learn/manipulating-the-dom-with-refs#accessing-another-components-dom-nodes)。
大概就是：

```
// React 版本：18.2.0
function Parent () {
  const cRef = useRef(null)
  // 通过 cRef.current 可以拿到子组件 Child
  return <Child ref={cRef} />
}
const Child = forwardRef(function (props, ref) {
  // 接收到 ref，然后转发到 div 上
  return <div ref={ref}>这里是子组件</div>
})
```

使用 useRef 和 forwardRef 拿到子组件之后，还是不能直接调用子组件的方法，需要子组件使用 useImperativeHandle 来提供可供父组件调用的函数/变量。具体使用方法看[这里](https://beta.reactjs.org/reference/react/useImperativeHandle#useimperativehandle)。
大概就是：

```
const Child = forwardRef(function (props, ref) {
  useImperativeHandle(ref, () => ({
    add: () => {}, // 在这里 return 出去的，父组件通过 ref 就可以直接拿到
    num: num
  }), [])
  return <div ref={ref}>这里是子组件</div>
})
```

[完整 Demo 看这里](https://codesandbox.io/s/parentusechildmethodinfn-zivkpj)。

## 自定义 props

通过自定义 props 可以避开函数组件没有实例无法提供 ref 的问题，在父组件上先用 `const xRef = useRef(null)` 新建一个 ref 变量，然后再给子组件添加 prop `<Child onRef={xRef} />` ，这样子组件内就可以通过 props.onRef 拿到父组件中的 xRef，然后使用 useImperativeHandle 来把方法提供出去。和上面的方法相比只是少了 forwardRef 的包裹。
[完整 Demo 看这里](https://codesandbox.io/s/parentusechildmethodwithpropsinfn-yw0e9z)

# 父子皆为 class 组件

## createRef + ref 赋值

父组件中使用 `this.xRef = createRef(null)` ，再给子组件赋值 `<Child ref={this.xRef} />` 即可直接调用 class 子组件内的所有方法。
[完整 Demo 看这里](https://codesandbox.io/s/parentusechildmethodinclass-dx40tb)。

## 回调 refs

不需要事先使用 createRef 创建 ref 变量，直接在子组件上赋值 `<Child ref={x => this.xRef = x} />` ，即可直接通过 xRef 调用子组件内所有方法。[React 在组件挂载时，会调用 ref 回调函数并传入 DOM 元素，当卸载时调用它并传入 null。在 componentDidMount 或 componentDidUpdate 触发前，React 会保证 refs 一定是最新的。](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html#callback-refs)

## 自定义回调函数 props

自定义 props 的好处在于 props 的可操作性，无视 HOC 和组件嵌套，你甚至可以通过 props 透传拿到孙组件的 ref 来进行操作。
父组件上给 Child 设置一个自定义的 prop `onRef = {ele => this.xRef = ele}`，在子组件内，componentDidMount 里添加 `this.props?.onRef?.(this)`，将子组件的 this 绑定到父组件的 this.xRef 上，这样就可以在父组件中，通过 this.xRef 拿到子组件的实例，从而调用子组件的方法。
[完整 Demo 看这里](https://codesandbox.io/s/parentusechildmethodwithpropsinclass-j39gdq?file=/src/App.js)

# 其他

对于父组件为 class 子组件为 function，或者父组件为 function 子组件为 class，对上述方式进行组合即可。

父子组件通信看这里：{% post_link 父子组件通信 %}

# 参考资料

-   [react 父组件调用子组件的方法小结](https://www.haorooms.com/post/react_class_hooks_dy)
-   [Refs and the DOM](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html)
-   [react 中父组件调用子组件的方法](https://blog.csdn.net/qq_36990322/article/details/109858890)
-   [this.props.onRef(this)](https://github.com/kriasoft/react-starter-kit/issues/909#issuecomment-284423034)
-   [How to call child component method from parent?](https://github.com/kriasoft/react-starter-kit/issues/909)
-   [React 中 render props 和 onRef 比较](http://huangruichang.github.io/?techniques/render-props-vs-onRef/index)
