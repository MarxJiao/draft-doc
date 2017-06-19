# 富文本样式

既然我们已经创建了基础的顶层api，现在我们可以更进一步，来看一下基础的富文本样式是怎么添加到`Draft`编辑器中的。

可以参考这个[例子](https://github.com/facebook/draft-js/tree/master/examples/draft-0-10-0/rich)。

# EditorState：你的命令

上一篇文章中介绍了`EditorState`对象是编辑器状态的快照，并且通过onChange props能获取到新的`EditorState`。

然而，你的上层React组件维护了编辑器的状态，你还可以以合适的方式来更改编辑器的`EditorState`。

对于内联和块级的样式行为，RichUtils模块提供了许多有用的函数来帮助处理状态。

同样的，Modifier模块也提供了许多常见操作，允许你对编辑状态进行修改，包括文本、样式等的修改。这个模块是一组编辑函数，可以实现更简单更小的编辑功能，返回编辑器所需的`EditorState`对象。

对于这个例子，我们将使用`RichUtils`来演示如何在上层组件中对编辑器应用富文本样式。

# RichUtils和键盘命令
`RichUtils`包含了一些键盘命令的信息，比如`Cmd + B`(粗体)，`Cmd + I`(斜体)等。

我们能通过prop`handleKeyCommand`来监听和处理键盘命令，并将它们绑定到RichUtils中以应用或删除所需的样式。

```javascript
import {Editor, EditorState, RichUtils} from 'draft-js';

class MyEditor extends React.Component {
  constructor(props) {
    super(props);
    this.state = {editorState: EditorState.createEmpty()};
    this.onChange = (editorState) => this.setState({editorState});
    this.handleKeyCommand = this.handleKeyCommand.bind(this);
  }
  handleKeyCommand(command) {
    const newState = RichUtils.handleKeyCommand(this.state.editorState, command);
    if (newState) {
      this.onChange(newState);
      return 'handled';
    }
    return 'not-handled';
  }
  render() {
    return (
      <Editor
        editorState={this.state.editorState}
        handleKeyCommand={this.handleKeyCommand}
        onChange={this.onChange}
      />
    );
  }
}
```

>提供给handleKeyCommand的command参数是一个字符串值，即要执行的命令的名称。这是从DOM的键盘事件映射的。更多的信息，以及为什么函数返回处理或未被处理的详细信息，请参阅高级主题-键盘绑定。

# 在UI中控制样式

在我们的React组件中，你能够添加按钮或者其它控制元素来允许用户在编辑器中控制样式。在上面的例子中，我们使用键盘来绑定命令，但是我们也能够通过添加很复杂的UI元素来支持这些丰富的样式。

这有一个非常简单的例子，使用`Bold`按钮来切换文本的粗体样式。

```javascript
class MyEditor extends React.Component {
  // …

  _onBoldClick() {
    this.onChange(RichUtils.toggleInlineStyle(this.state.editorState, 'BOLD'));
  }

  render() {
    return (
      <div>
        <button onClick={this._onBoldClick.bind(this)}>Bold</button>
        <Editor
          editorState={this.state.editorState}
          handleKeyCommand={this.handleKeyCommand}
          onChange={this.onChange}
        />
      </div>
    );
  }
}

```