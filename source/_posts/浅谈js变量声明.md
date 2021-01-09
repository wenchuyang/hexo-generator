---
title: 浅谈js变量声明
date: 2018-04-08 21:25:48
tags: JavaScript
categories:
- 计算机
- 前端
---

&emsp;&emsp;或者确切地说应该是变量声明以及与之相关的一些东西。<br>
&emsp;&emsp;这里只谈四种声明方式（var,function,let,const），其它的声明方式以后有接触再更新。<br>
## 'var' VS 'let'
&emsp;&emsp;var貌似是最常用的，在ES6还没出来的时候只能用var来声明一个变量，var声明的变量只有函数能对其构成作用域。这样的话会出现一些意料之外的问题，比如如果你的页面里有一个元素的id是xxx，那么你在控制台打印出来的就是xxx这个元素本身，但是如果这个id名是parent，那么它打印出来的就是window全局属性。如果你在控制台再输入`var parent = document.getElementById('parent')`，此时console.log得到的结果依然是parent这个元素本身。为什么呢？<br>
&emsp;&emsp;全局变量。是的，答案是全局变量。在我们还没有写代码的时候，浏览器就为我们提供了一个全局对象window，window里边的属性我们称之为全局属性。其中parent就是window的一个全局属性，所以在我们还没有写id=parent的时候，parent就已经存在了。所以我们直接调用parent并不能得到那个元素。<br>
&emsp;&emsp;但是，你也知道，如果你写了下边这两句话<br>
```
var a = 10
var a = 20
```
&emsp;&emsp;得到的答案是20，因为变量存在覆盖。所以，在我们重新声明`var parent = document.getElementById('parent')`的时候，实际上是把全局变量window.parent覆盖了。鉴于覆盖全局变量可能会引发另外的问题，所以变量名尽量不要用window的属性。但是window属性那么多——<br>
&emsp;&emsp;所以使用局部变量。如果只是在一个小的范围内声明并使用一个变量，那么这个变量在这个区域内把全局属性覆盖了也基本上不会出什么问题。<br>
只有函数能构成var的作用域，所以要想使用一个局部变量，我们就需要声明并执行一个函数。这就是**立即执行函数**。是不是很熟悉？所以说最讨厌这些概念了，明明很简单的东西，非得喊个听上去很牛逼的名词。<br>
&emsp;&emsp;鉴于我们写这个函数的目的只是为了造成一个局部作用域，并不需要后边继续进行调用，所以为了提高效率（也为了让你少费脑子想一个函数名），选择直接在声明之后执行它。<br>
```
function(){
  var parent = document.getElementById('parent')
  console.log(parent)
}.call()
```
&emsp;&emsp;但是这样子的话并不是万无一失的，在你刷新页面之后会报错，浏览器以为我们的语法错了。避免浏览器报错的方法很多，但是万变不离其宗，不论使用的是什么方法，我们的目的都是让浏览器知道这是一条语句，而不是别的什么。所以这样子给整个函数及其调用方法加一个小括号可以做到。<br>
```
(
  function(){
    var parent = document.getElementById('parent')
    console.log(parent)
  }.call()
)
```
&emsp;&emsp;或者只在函数声明上加小括号也行。比较通用的是在函数前边加一个运算符，比如说`+`，`-`，`!`（取反），`~`（二进制取反），告诉浏览器后边的是一个值而不是一个函数声明，让浏览器声明并调用求值。虽然这样得到的函数返回值与原先的结果可能会有偏差（比如用-号的话得到一个负值），但是我们并不需要这个返回值。一开始就说过，这个函数只是用来造一个局部作用域。<br>
&emsp;&emsp;你一定会认为这样很麻烦。不过是想要用一个局部变量而已，这样大费周章多少让人心有不甘。所以ES6出了一个`let`声明方法。<br>
&emsp;&emsp;let的作用域在包裹着它的代码块里，也就是说，如果你想要使用一个局部变量，并不需要声明一个立即执行函数，而只需要写一个代码块。上边的代码完全可以变成这个样子<br>
```
{
  let parent = document.getElementById('parent')
  console.log(parent)
}
```
&emsp;&emsp;在区域外parent依旧指的是window.parent。这就是let。<br>
&emsp;&emsp;此外，关于var还有一个很容易犯的错误，用let就可以完全避免。用js通过父元素为子元素绑定click事件。<br>
html如下：
```
<div class="buttons" id="buttons">
  <span>按钮0</span>
  <span>按钮1</span>
  <span>按钮2</span>
  <span>按钮3</span>
</div>
```
js如下：
```
var children = buttons.children   //用var声明了一个全局变量children，储存buttons的所有子元素
for(var i=0; i<children.length; i++){
  children[i].onclick = function(){    //执行点击事件的时候输出对应的第几个按钮
    console.log(i)  
  }
}
```
&emsp;&emsp;我们想要得到的效果是点击按钮0的时候输出0，点击按钮1的时候输出1...但事实上无论你点击什么，输出结果都是4，因为内存里只存了一个`i`，这个i的最终运算结果是4。但是如果你把`var i = 0`改成`let i = 0`就可以得到你想要的结果。因为如果你使用let的话，每次循环引用的都是不同的i（引用了i变量的不同实例），故而能实现你的需求。<br>
## 变量提升
&emsp;&emsp;var和function都有变量提升机制，也就是说，你可以先使用一个变量，然后再声明它。<br>
**var的变量提升**：
```
console.log(a)  //undefined
var a = 10
```
&emsp;&emsp;var的变量提升会把声明部分提升到前边。所以上边的代码真正的运行顺序应该是<br>
```
var a
console.log(a)
a = 10
```
**function的变量提升**：
```
console.log(f)  //f(){}
function f(){}
```
&emsp;&emsp;function的变量提升会把整个函数提升到前边，所以上边代码的真正运行顺序应该是<br>
```
function f(){}
console.log(f)
```
&emsp;&emsp;我们知道函数还可以用`var f = function(){}`来声明，这种声明方式的话事实上是<br>
```
var f
f = function(){}
```
&emsp;&emsp;所以变量提升依旧只是提升`var f`。<br>
**let没有变量提升**
&emsp;&emsp;是的，let算是比较正常的了，它不存在变量提升。也就是说，浏览器不会那么~~贱~~多此一举地帮你改变代码的顺序。但是这样的话就会出现“暂时性死区（temporal dead zone）”，如果你用let在一个作用域内声明了一个变量，那么在这个作用域内，let声明之前，这个变量是不可以被使用的，会报错。但是由于js可以有非声明变量（不使用任何声明语句直接对一个变量进行操作），所以这就会出现一个很奇怪（发现自己写js相关blog的时候用“奇怪”的频率好高。。。）的现象。<br>
```
typeof a  //error: a is not defined
a = 20  //error: a is not defined
typeof a  //error: a is not defined
let a = 10
```
&emsp;&emsp;但是上述代码如果把`let a = 10`删掉就会是这样的结果。<br>
```
typeof a  //"undefined"
a = 20   //不报错，正常赋值
typeof a  //"number"
```
&emsp;&emsp;除了暂时性死区之外，还需要注意的一点是let声明的变量`不可重复声明`。在同一个代码块内，如果你用let声明了一个变量a，那你就不能再继续用任何声明方法（包括let，var和function）来声明a变量。<br>
## const声明常量
&emsp;&emsp;使用const声明的变量往往是一个常量，但是这个常量只是说它们在栈内存中存储的东西不变，对于复杂类型（object）而言，const意味着这个变量里边存的地址不会改变，但是地址里边的东西其实是可变的。除此之外，const的特性与let基本相同。两者同是ES6新增的命令。<br>
```
const N = 10  //常量一般用全大写字母表示
N = 20  //error
const OBJ = {
  'a': 'ccc',
  'b': 'bbb'
}
OBJ = {}  //error
OBJ['a'] = 'aaa'  //成功赋值
```