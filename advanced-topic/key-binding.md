# 快捷键绑定

`Editor`组件通过`keyBindingFn`这个prop提供了自定义按键绑定的功能。允许你通过键盘命令来操作你的编辑器。

# 默认快捷键

默认的快捷键函数是`getDefaultKeyBinding`。

由于Draft.js对DOM的渲染和行为的严格控制，基本的快捷键命令需要通过按键绑定系统实现。

`getDefaultKeyBinding`方法将系统级的快捷键映射到`DraftEditorCommand`字符串，用于处理组件的行为。

例如，`Ctrl+Z` (Win)和`Cmd+Z` (OSX) 对应'undo'命令，组件收到'undo'命令就会执行`EditorState.undo()`。

# 自定义快捷键

可以使用自定义快捷键函数来提供自定义快捷键命令。

推荐使用`getDefaultKeyBinding`作为垫背的方案，以便你的编辑器可能受益于默认命令。

使用您的自定义命令字符串，您可以实现handleKeyCommand prop函数，它允许您将该命令字符串映射到所需的行为。 如果handleKeyCommand返回'handled'，则该命令被视为处理。 如果它返回'not-handled'，则命令将通过。

# 示例

假如我们有个编辑器，我们应该定期保存内容到服务器上。

首先我们来定义按键绑定函数：

```javascript
import {getDefaultKeyBinding, KeyBindingUtil} from 'draft-js';
const {hasCommandModifier} = KeyBindingUtil;

function myKeyBindingFn(e: SyntheticKeyboardEvent): string {
    if (e.keyCode === 83 /* `S` key */ && hasCommandModifier(e)) {
        return 'myeditor-save';
    }
    return getDefaultKeyBinding(e);
}
```

我们的函数接收一个按键事件，之后我们检查它是否匹配我们的条件：首先必须是's'按键，之后它必须有一个命令修饰符(例如，在OSX上是'cmd'，在windows上是「ctrl」).

如果按键匹配，就返回这个命令名。否则就进入默认的按键处理。

在我们的编辑器组件中，我们可以使用`handleKeyCommand`这个prop:

```javascript
import {Editor} from 'draft-js';
class MyEditor extends React.Component {

  constructor(props) {
    super(props);
    this.handleKeyCommand = this.handleKeyCommand.bind(this);
  }
  // ...

  handleKeyCommand(command: string): DraftHandleValue {
    if (command === 'myeditor-save') {
      // 这里可以发送个保存内容的请求，设置个新的editorState等等
      return 'handled';
    }
    return 'not-handled';
  }

  render() {
    return (
      <Editor
        editorState={this.state.editorState}
        handleKeyCommand={this.handleKeyCommand}
        keyBindingFn={myKeyBindingFn}
        ...
      />
    );
  }
}
```

'myeditor-save'命令能够被用来做一些自定义操作，之后返回'handled'来告诉编辑器，这个命令已经被处理了不需要再做其它操作了。

通过返回'not-handled'或者其它情况，默认处理程序就会处理默认的行为。