# 官网介绍
开发框架：
1. 基于组件，用于写 web 应用
2. 收集了很多功能，路由、表单管理、客户端服务器通信
3. 一系列开发者工具来帮助你进行开发

## Components
```
import { Component } from '@angular/core';

@Component({
  selector: 'hello-world',
  template: `
    <h2>Hello World</h2>
    <p>This is my first component!</p>
    `,
})
export class HelloWorldComponent {
}
```
页面组件化，组件可以是单个的页面，也可以是页面的某个公有节点。

## Templates
```
@Component ({
  selector: 'hello-world-interpolation',
  templateUrl: './hello-world-interpolation.component.html'
})
export class HelloWorldInterpolationComponent {
}
```
可以直接使用 template 放 html 字符串，也可以使用 templateUrl 放一个 html 文件。
