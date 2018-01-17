# Block样式

在`Editor`中，一些block类型已经给了默认的css样式，来提供自定义编辑器基础启动和运行。

通过为`Editor`定义一个blockStyleFn pros，可以指定在渲染时应用到这个block的css class.

# DraftStyleDefault.css

Draft.js将默认的block样式包含在了`DraftStyleDefault.css`里。（注意，CSS类名的注释是Facebook内部CSS管理模块生成的。）

这些CSS规则主要用于提供默认的样式，没有哪个调用方会管理默认的样式。

# blockStyleFn

`Editor`上的`blockStyleFn` prop允许你在渲染时使用自定CSS样式。例如，你可能会希望类型为「blockquote」的block带上斜体文本。

```javascript
function myBlockStyleFn(contentBlock) {
  const type = contentBlock.getType();
  if (type === 'blockquote') {
    return 'superFancyBlockquote';
  }
}

import {Editor} from 'draft-js';
class EditorWithFancyBlockquotes extends React.Component {
  render() {
    return <Editor ... blockStyleFn={myBlockStyleFn} />;
  }
}
```

之后在你的CSS中这样写：

```css
.superFancyBlockquote {
  color: #999;
  font-family: 'Hoefler Text', Georgia, serif;
  font-style: italic;
  text-align: center;
}
```