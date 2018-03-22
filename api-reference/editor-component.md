# Editor Component

这篇讨论核心的编辑器组件`Editor`的 API 和 props。props在`DraftEditorProps`中定义。

## Props

### 基础

查看[基础API](../quick-start/api-basics.md)中的介绍。

#### editorState

```javascript
editorState: EditorState
```

`EditorState`会被`Editor`组件渲染。

#### onChange

```javascript
onChange: (editorState: EditorState) => void
```

当编辑或者选择改变时，`onChange`被`Editor`触发。

### 进阶(可选的)

#### placeholder

```javascript
placeholder?: string
```

可选的placeholder字符串用来在编辑器为空时显示。

注意：你可以使用css来定义placeholder的样式或者隐藏它。例如，在[富文本编辑器](https://github.com/facebook/draft-js/tree/master/examples/draft-0-10-0/rich)中，placeholder会在用户改变空编辑器block样式的时候隐藏。这是因为placeholder可能在样式改变的时候可能不会与光标对齐。

#### textAlignment

```javascript
textAlignment?: DraftTextAlignment
```

可以覆盖编辑器的文本对齐方式。这个对齐的值会应用到整个内容，不管编辑器默认的文本对齐方式如何。

是可以使用它来居中或者向某个方向对齐文本，来适应你的UI设计。

如果没设置这个值，文本的对齐方式就会在块级元素内，根据字符来判断。

#### textDirectionality

```javascript
textDirectionality?: DraftTextDirectionality
```

可以覆盖编辑器文本的方向。可以使用的值为：「RTL」从右到左，就像希伯来语和阿拉伯语；「LTR」从左到右，像英语、西班牙语等。这个方向会应用到全部的内容上，不管输入语言默认的方向是什么样的。

如果没设置这个值，文本的方向就会在块级元素内，根据字符来判断。

#### blockRendererFn

```javascript
blockRendererFn?: (block: ContentBlock) => ?Object
```

设置一个函数来自定义块级渲染。查看[高级教程/自定义blocK渲染](../advanced-topic/custom-block-rendering.md)来获取细节和使用信息。

#### blockStyleFn

```javascript
blockStyleFn?: (block: ContentBlock) => string
```

设置一个函数来自定义class名来应用到给定的block。查看[Block样式](../advanced-topic/block-styling.md)来获取细节和使用信息。

#### customStyleMap

```javascript
customStyleMap?: Object
```

定义一些行内样式来应用到文本上。查看[复杂的行内样式](../advanced-topic/complex-inline-styles.md)来获取细节和使用信息。

#### customStyleFn

```javascript
customStyleFn?: (style: DraftInlineStyle, block: ContentBlock) => ?Object
```

定义一个函数来将应用到行内文本的样式转换为css对象。查看[复杂的行内样式](../advanced-topic/complex-inline-styles.md)来获取细节和使用信息。

### 行为（可选）

#### autoCapitalize?: string

```javascript
autoCapitalize?: string
```

设置是否自动大写和它怎样表现。更多关于平台的能力和使用请[查看mdn](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/Input#attr-autocapitalize)。

#### autoComplete?: string

```javascript
autoComplete?: string
```

设置是启用自动完成和自动完成的表现。更多关于平台的能力和使用请[查看mdn](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/Input#attr-autocomplete)。

#### autoCorrect?: string

```javascript
autoCorrect?: string
```

设置是启用自动修正和它怎样表现。更多关于平台的能力和使用请[查看mdn](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/Input#attr-autocorrect)。

#### readOnly

```javascript
readOnly?: boolean
```

设置编辑器是否以静态dom的形式渲染，不可编辑。

这在自定义块级组件交互和只想展示内容时十分有用。

#### spellCheck

```javascript
spellCheck?: boolean
```

设置是否打开拼写检查。

注意在OSX Safari中，打开拼写检查就打开了自动修正，不管有没有开启自动正。也要注意IE中自动检查是永远被禁止的，因为IE不能触发拼写检查事件。

默认值是false。

#### stripPastedStyles

```javacript
stripPastedStyles?: boolean
```

设置是否去掉粘贴文本中的样式。

应该在你的编辑器不支持富文本样式时使用。

默认是false。

### DOM和辅助功能（可选）

#### tabIndex
#### ARIA props

这些props允许你在编辑器上设置可访问的属性。查看[DraftEditorProps](https://github.com/facebook/draft-js/blob/master/src/component/base/DraftEditorProps.js)来获取支持的属性的详细信息。

#### editorKey

```javascript
editorKey?: string
```

如果你不在服务端渲染`Editor`组件的话，你可能不需要设置这个prop。如果使用服务端渲染的话，你一定要设置这个prop来避免服务端和客户端的不匹配。

如果没设置的话，editorKey会在渲染时自动生成，并作为prop添加到编辑器的`<DraftEditorContents />`组件。如果你设置了这个prop，请确保每个编辑器的key值是唯一的。比如在粘贴文本时是否使用样式，就会用到这个值。

#### 可取消的Handlers 
