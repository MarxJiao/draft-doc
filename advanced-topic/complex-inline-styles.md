# 复杂的行内样式

在你的编辑器中，你可能希望在bold/italic/underline之外有更丰富的行内样式。例如，你可能想要不同的颜色、字体等。更进一步地，你可能需要相互重叠或者排斥的样式。

[Rich Editor](http://github.com/facebook/draft-js/tree/master/examples/draft-0-10-0/rich)和[ColorFul Editor](http://github.com/facebook/draft-js/tree/master/examples/draft-0-10-0/color)例子示范了复杂的行内样式。

## 模型

在Draft模型中，行内样式是建立在字符级别的。使用的是不可变的`OrderedSet`来定义摸个字符的样式。这些样式用字符串描述。（更多信息[CharacterMetadata](api-reference/charactermetadata.md)）

例如，看一下这段文本"Hello **world**"。前6个字符是空的set,相当于`OrderedSet()`。后面的5个字符相当于`OrderedSet.of('BOLD')`。为了便于理解，我们可以把`OrderedSet`想象成数组，虽然我们使用的是不可变的对象。

在本质上，我们的样式是这样的：

```javascript
[
  [], // H
  [], // e
  ...
  ['BOLD'], // w
  ['BOLD'], // o
  // etc.
]
```

## 重叠样式

现在我们让中间的字符显示为斜体: "He*llo **wo*rd** "。 我们可以使用[Modifier](api-reference/modifier.md) Api实现。

`OrderedSet`中使用了「ITALIC」。

```javascript
[
  [], // H
  [], // e
  ['ITALIC'], // l
  ...
  ['BOLD', 'ITALIC'], // w
  ['BOLD', 'ITALIC'], // o
  ['BOLD'], // r
  // etc.
]
```

Draft.js在渲染的时候会分析相邻的字符，并将相同样式的字符使用一个 `span`标签包裹。

## 将样式字符串对应到CSS

默认情况下，`Editor`提供了一些基础行内样式：'BOLD', 'ITALIC', 'UNDERLINE' 和 'CODE'。它们对应着简单的CSS样式对象，来将样式附加到文本上。

你可能会保留默认样式并添加自己的样式，也可能用自己的样式覆盖默认样式。

你可以通过为`Editor`提供一个`customStyleMap` prop来定义自己的样式对象。（查看[多彩编辑器](https://github.com/facebook/draft-js/tree/master/examples/draft-0-10-0/color)示例）

例如你可能想添加一个叫'STRIKETHROUGH'的样式。为了做到这点，我们先定义一个样式的map:

```javascript
import {Editor} from 'draft-js';

const styleMap = {
  'STRIKETHROUGH': {
    textDecoration: 'line-through',
  },
};

class MyEditor extends React.Component {
  // ...
  render() {
    return (
      <Editor
        customStyleMap={styleMap}
        editorState={this.state.editorState}
        ...
      />
    );
  }
}
```

在渲染的时候`textDecoration: line-through`样式会添加到所有的使用`STRIKETHROUGH`的字符。
