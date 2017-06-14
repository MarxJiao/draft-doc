# 概要

Draft.js是一个使用React构建富文本编辑器的框架，使用不可变的模型并且抽象了各种浏览器之间的差异。

不管你是简单的给几行可编辑的文本添加样式，还是构建一个复杂的完整的富文本编辑器，使用 Draft.js都很容易做到。

[这个链接](https://youtu.be/feUYwoLhE_4) 是2016年2月[React.js Conf](http://conf.reactjs.com/)上介绍Draft.js的内容。



# 安装

Draft.js发布在npm上，依赖React和React DOM，安装时这两个包也要装好。

```
npm install --save draft-js react react-dom

# 或者

yarn add draft-js react react-dom
```

Draft.js使用es6语法开发，一些浏览器上不支持。可以使用一些shim和polyfill来解决。

```
npm install --save draft-js react react-dom babel-polyfill

# 或者
yarn add draft-js react react-dom es6-shim
```

Learn more about[using a shim with Draft](https://draftjs.org/docs/advanced-topics-issues-and-pitfalls.html#polyfills).

# 注意api改变

在开始之前，我们需要注意最近我们要改变Draft中Entity的API。在v0.10.0版本中我们同时支持新api和旧api。在版本v0.11.0中我们将去掉对旧api的支持。如果你有兴趣帮助解决问题或者跟进项目进展，可以订阅[issue 839](https://www.gitbook.com/book/marxjiao/draft-js/edit#)

# 使用

```
import React from 'react';
import ReactDOM from 'react-dom';
import {Editor, EditorState} from 'draft-js';

class MyEditor extends React.Component {
  constructor(props) {
    super(props);
    this.state = {editorState: EditorState.createEmpty()};
    this.onChange = (editorState) => this.setState({editorState});
  }
  render() {
    return (
        <Editor editorState={this.state.editorState} onChange={this.onChange} />
    );
  }
}

ReactDOM.render(
  <MyEditor />,
  document.getElementById('container')
);
```

因为 Draft.js 支持 unicode，所以你必须把下面的meta标签添加到你html页面的&lt;head&gt;&lt;/head&gt;中。

```
<meta charset="utf-8" />
```

在渲染编辑器前我们需要引用`Draft.css` 。Learn more about[why](https://draftjs.org/docs/advanced-topics-issues-and-pitfalls.html#missing-draft-css).

接下来我们将学习Draft.js的基础api和你能用Draft.js做什么事情。

  




