# 自定义block组件

Draft旨在解决简单的富文本编辑问题(例如评论和聊天消息)，但它有能里提供更强大的富文本编辑功能，例如[Facebook Note](https://www.facebook.com/notes/)。

用户能够通过上传图片或者引入facebook photos里的图片，来把图片嵌入到笔记中。为此，Draft支持自定义块级组件，来渲染媒体内容。

Draft代码仓库中的[TeX editor](https://github.com/facebook/draft-js/tree/master/examples/draft-0-10-0/tex)提供了一个自定义组件渲染的例子。示例中的公式编辑器通过[KaTeX](https://khan.github.io/KaTeX/)实现。

还有一个[媒体示例](https://github.com/facebook/draft-js/tree/master/examples/draft-0-10-0/media)展示了一些自定义渲染音频、图片、视频的例子。

通过使用自定义组件渲染，可以在编辑器内实现丰富的交互内容。

## Custom Block Components

在`Editor`组件中，有一个`blockRendererFn`prop。这个prop函数允许通过type、text或其它信息自定义组件渲染`ContentBlock`对象。

例如，我们希望使用`MediaComponent`来渲染type为atomic的`ContentBlock`。

```javascript
function myBlockRenderer(contentBlock) {
  const type = contentBlock.getType();
  if (type === 'atomic') {
    return {
      component: MediaComponent,
      editable: false,
      props: {
        foo: 'bar',
      },
    };
  }
}

// 之后...
import {Editor} from 'draft-js';
class EditorWithMedia extends React.Component {
  ...
  render() {
    return <Editor ... blockRendererFn={myBlockRenderer} />;
  }
}
```

如果blockRendererFn中没有返回渲染对象，`Editor`会使用默认的`DraftEditorBlock`渲染。

`component`属性定义了要使用的组件，`props`是要传递给组件的数据，可以通过`props.blockProps`访问到。此外，`editable`设置了组件是否是`contentEditable`。

如果你的自定义组件中不包含文本，强烈建议将editable设置为false。

如果你的组件中包含`ContentState`，并且你希望可以修改它们，你的组件中需要使用`DraftEditorBlock`组件。这样确保Draft能够正常处理光标的状态。

通过在高阶组件中定义函数，通过props传递给自定义组件，自定义组件中可以使用这些函数。

# 定义自定义组件

在`MediaComponent`中，最重要的一点是我们要取回在entity中保存的元数据来渲染。在管理`EditorState`的时候，你可能已经在type为atomic的block上关联了相关的entity key。你可以在自定义组件的render函数中取回这些数据。

```javascript
class MediaComponent extends React.Component {
  render() {
    const {block, contentState} = this.props;
    const {foo} = this.props.blockProps;
    const data = contentState.getEntity(block.getEntityAt(0)).getData();
    // Return a <figure> or some other content using this data.
  }
}
```

`ContentBlock`对象和`ContentState`记录在自定义组件中都是可以通过props访问到的。通过`ContentState`和`Entity` map，你能够获取到自定义组件所需的信息。

> 从block中检索entity的确是一个很尴尬的api，支持重新审视。

# 推荐做法和其它信息。

如果你的组件需要鼠标交互，在交互过程中将`Editor`设置为`readOnly={true}`是很明智的。这样就不会在交互过程中带来selection的改变。这样就不会带来自定义组件和编辑器默认状态更新产生冲突的问题。

上边这条建议在在定义组件包含文本框的时候显得尤为重要，就像Tex editor示例那样。

值得注意的是，在Facebook Notes编辑器中，我们并没有使用特殊的SelectionState，例如在选择图片时高亮。这是因为媒体本身提供了丰富的交互，例如调整大小和用于鼠标行为的其它控件。

使用Draft的开发者能够完全控制原生的selection api，在媒体中使用selection也是能够实现的。我们没有在Facebook中处理这个问题，所以没有在Draft中提供相应的解决方案。


