---
title: react获取元素
categories:
  - 计算机
  - 前端
tags:
  - react
  - ref
date: 2021-04-05 13:06:37
---

# 你真的要获取元素吗
正常情况下我们并不需要获取元素，如果需要修改某个组件，一般情况下是通过父组件传递的 props 或者自己本身的 state 来重新渲染。
但是如果要获取元素， react 同样提供了一些方法。

# 使用 ref 获取元素

## class 组件内部
  1. 创建 ref `this.myRef = React.createRef()`
  2. 绑定 ref `<div ref={this.myRef}></div>`
  3. 获取元素 `const ele = this.myRef.current`
    ```
    class CBoxInner extends React.Component {
      constructor(props) {
        super(props);
        this.myRef = React.createRef(); // 创建 ref
        this.handleClick = ()=>{
          console.log(this.myRef.current) // 打印出 DOM 元素
        }
      }
      render() {
        return (
          <div>
            <div ref={this.myRef}>Box1 div</div> // ref 绑定
            <button onClick={this.handleClick}>
              console log Box1
            </button>
          </div>
        );
      }
    }
    ```

## class 组件外部
   
### 暴力获取
将 ref 绑定在你引用的 class 组件上，不同的是此时的 ref.current 是**组件的实例**。
如果你需要在父组件获取子组件的某个元素，将父组件的 ref 绑定到子组件上，然后通过父组件的 ref 获取子组件的 ref，从而拿到子组件的元素。`this.myRef.current.xxxRef.current`
  ```
  <CBoxInner ref={this.myRef} />
  ```
### 通过属性传递
相当于 function 组件的 refs 转发（forwardRef）。
1. 在父组件创建 ref `this.cBox = React.createRef()`
2. 自定义一个属性（x），通过这个属性给子组件传递 ref 。`<CBoxInner x={this.cBox} />`
3. 在子组件中给这个 ref 赋值。`<div ref={this.props.x} />`
4. 此时你在父组件中直接使用 `this.cBox.current` 获取的就是子组件的元素。

### 借用高阶组件的思想
高阶组件指的是一个函数，接收一个组件，返回一个新的组件。借用这个函数我们可以创建组件。
上面我们知道，借用属性传递可以传递 ref，但是不能直接传递名称为 ref 的属性。所以我们在中间封一层函数组件。
```
class HOCBoxInner extends React.Component {
  render() {
    const { forwardRef } = this.props;
    return <div ref={forwardRef} />;
  }
}
const HOCBox1 = React.forwardRef((props, ref) => {
    return <HOCBoxInner {...props} forwardRef={ref} />;
  });
```
此时我们拿到的 HOCBox1 就可以直接传递 ref。`<HOCBox1 ref={this.ref} />`
因为这样子同时生成了两个组件，一个 HOCBoxInner 一个 HOCBox1.
想一想，高阶组件是传递一个组件，返回一个新的组件。如果我们不需要复用的话，那么传递一个组件这个步骤就可以省去了。直接在函数内部创建一个 class 组件。如果你甚至不想暴露函数的话，让它立即执行。
```
const HOCBox = (function createHOCBox() {
  class HOCBoxInner extends React.Component {
    render() {
      const { forwardRef } = this.props;
      return <div ref={forwardRef} />;
    }
  }
  return React.forwardRef((props, ref) => {
    return <HOCBoxInner {...props} forwardRef={ref} />;
  });
}())
```
这样我们拿到的 HOCBox 就是一个可以传 ref 的组件了。

## function 组件内部
1. 创建 ref `const myRef = useRef();`
2. 绑定 ref `<div ref={myRef}></div>`
3. 获取元素 `const ele = this.myRef.current`
```
 function FBoxInner() {
   const myRef = useRef();
   const handleClick = () => {
     console.log(myRef.current);
   };
   return (
     <div>
       <div ref={myRef}>function div</div>
       <button onClick={handleClick}>console log function</button>
     </div>
   );
 }
```

## function 组件外部
class 组件外部使用 ref 获取的是 class 组件的实例，而 function 组件没有实例，所以无法使用 `ref={myRef}` 来绑定获取。
function 组件使用 forwardRef 来实现。
function 组件声明一般是 `function Xxx(props){}` , 使用 forwardRef 的话，是这样 `const Xxx = React.forwardRef((props, ref)=>{})`. 我们可以直接在参数中获取父组件传递过来的 ref 。
1. 父组件创建 ref 。`this.mref = React.createRef()`
2. 父组件给子组件传递 ref `<Child ref={this.mref} />`
3. 子组件给 ref 赋值 `<div ref={ref} />`

## 使用回调函数获取
使用回调函数获取 ref 的话，function 组件和 class 组件使用方法一致。
```
function CBFBoxInner() {
  let myRef = null;
  const setRef = (el) => (myRef = el);
  const handleClick = () => {
    console.log(myRef);
  };
  return (
    <div>
      <div ref={setRef}>callback function div</div>
      <button onClick={handleClick}>console log callback function</button>
    </div>
  );
}
```
需要注意的是此时获取的 myRef 就是元素本身，不需要 myRef.current . 

--------

[findDOMNode()](https://zh-hans.reactjs.org/docs/react-dom.html#finddomnode) 同样可以获取元素，但是不推荐使用，且严格模式下该方法已经弃用。
[ref 获取元素 Demo](https://codesandbox.io/s/ref-get-element-3hn9o?file=/src/App.js)

*受控组件 VS 非受控组件*
在一个表单中，受控组件指的是表单元素的值完全由 state 控制，非受控组件指的是表单元素的值由它本身控制。
比如一个 input 框，我输入一个 "xxx" 。
受控组件是我给它绑定一个 onChange 事件，一旦它发生了改变，我就修改 state 的值，而因为我修改了 state 的值，所以这个 input 的 value 就改变了。
非受控组件是我给它绑定一个 ref ，在我需要的时候直接通过获取 DOM 元素的 value 来获取它的值。`<input type="file" />` 始终是一个非受控组件。