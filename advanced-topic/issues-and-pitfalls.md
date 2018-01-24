# 问题和陷阱

这篇文章记录一些Draft的已知问题和我们在Facebook使用这个框架时容易遇到的一些陷阱。

## 常见的陷阱

### 延迟的状态更新

一个单向数据管理常见的模式是使用setTimeout或其它机制批量或延迟数据状态的更新。状态更新后发送到相关的React组件来渲染。

当延迟被引入到使用Draft的React应用时，有可能造成比较大的交互问题。这是因为编辑器期望当用户输入时，状态以同步的方式立即更新和渲染。延迟会阻止更新内容通过编辑器向组件树传播，这会导致打字输入和内容更新之间断开连接。

为了避免这种情况，仍然使用延迟或者批量处理机制，你应该将编辑器状态的传播和延迟行为分开。也就是说，你必须允许你的`EditorState`传播到`Editor`组件，并且独立执行而不影响Editor组件状态的分批更新。

### 丢失Draft.css

Draft包含了少量的CSS资源，可以通过Draft.css使用。

当渲染编辑器的时候，这个css文件应该被包含在项目中，这个css文件设置了默认的文本对齐方式，空格和一些其它的重要特性。没有它，你可能会在块级定位，对齐，和光标表现上遇到问题。

如果你想写你自己的Draft.css，你可能需要复制大部分的默认样式。

## 已知问题

### 自定义OSX按键绑定

因为浏览器没有办法接触系统级自定义按键，所以没有办法拦截不和系统按键匹配的按键行为。

当自定义按键的用户在使用Draft Editor时，由于按键不匹配可能会出现一些问题。

### 浏览器插件和扩展

就像其它React应用一样，浏览器插件和扩展如果修改了DOM可能会造成Draft出错。

例如，语法检查器能够修改contentEditable元素中的DOM，添加类似下划线或者背的样式。如果DOM不符合预期，React没发协调DOM，编辑器状态可能无法和DOM保持一致。

某些旧的广告拦截器也会改变原生的DOM Selection API，（无论如何这都不是什么好的实践），由于Draft依赖这个API来维持选择的状态，这可能会为编辑器的交互带来麻烦。

### IE浏览器

从IE11开始，Internet Explorer在某些国际输入方法中表现出了一些问题，最明显的是韩文输入。

### Polyfills

一些Draft的代码和它的依赖使用了ES2015的语言特性。像`class`这样的语言特性在Draft使用Babel编译时就去掉了，但是并没有包含浏览器api的polyfill（例如`String.prototype.startsWith`）。我们期望你的浏览器原生支持这些特性，或者使用一些polyfill。我们在很多例子里用的是[es6-shim](https://github.com/es-shims/es6-shim)，当然你也可以根据情况来使用[babel-polyfill](https://babeljs.io/docs/usage/polyfill/)。

不管使用哪个polyfill或shim，你应该在你项目的入口文件之前包含他们（至少要在你引用Draft之前）。例如使用[create-react-app](https://github.com/facebookincubator/create-react-app)，并且目标浏览器是ie11，最好在`src/index.js`引入你的polyfill：

```javascript
import 'babel-polyfill';
// or
import 'es6-shim';

import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import './index.css';

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

### 暂不支持移动端

Draft.js正在像移动端靠拢，但暂时还没有官方支持移动浏览器。这里有一些在移动浏览器上的issues -- 查看标签为['android'](https://github.com/facebook/draft-js/labels/android)和['ios'](https://github.com/facebook/draft-js/labels/ios)的issues。