# 基础API

这里介绍Draft.js的基础api，也可以参考这个示例[working example](https://github.com/facebook/draft-js/tree/master/examples/draft-0-10-0/plaintext)

## 可控的输入框

## Controlled Inputs

React组件`Editor`作为一个可控的，内容可以被编写的组件，封装了一些顶层的api模型，和reac的input api非常类似。

我们先来看下，一个可控的input组件需要2个关键的地方：

1. 一个代表input状态的值
2. 一个`onChange` prop 来获取input的更新

我们可以通过`value`来控制input的属性，同时允许DOM根据用户的输入更新。

```javascript
class MyInput extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};
    this.onChange = (evt) => this.setState({value: evt.target.value});
  }
  render() {
    return <input value={this.state.value} onChange={this.onChange} />;
  }
}
```
顶层组件可以通过value属性控制input的状态。

# 可控制的富文本

设想一下，如果我们要开发一个富文本编辑器，首先我们面临2个问题：

1. 一串纯文本不足以表示富文本编辑器的复杂状态。
2. 在ContentEditable的元素中，没有像`onChange`这样的事件。

因此富文本编辑器的状态用一个不可边的对象EditorState表示，并且在编辑器的组件内部实现了一个`onChange`，以便将富文本编辑器的状态返回给上层组件。

EditorState对象是编辑器状态的完整快照，包括内容，光标和撤消/重做历史记录。编辑器中对内容和选择的所有更改将创建新的EditorState对象。注意在更新编辑器的editorState之前，原有的EditorState对象还是有效的。

```javascript
import {Editor, EditorState} from 'draft-js';

class MyEditor extends React.Component {
  constructor(props) {
    super(props);
    this.state = {editorState: EditorState.createEmpty()};
    this.onChange = (editorState) => this.setState({editorState});
  }
  render() {
    return <Editor editorState={this.state.editorState} onChange={this.onChange} />;
  }
}
```

对于编辑器中发生的任何更改，`onChange`处理程序都会传入的都是最新的EditorState。



