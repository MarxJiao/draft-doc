# Decorators

我们的富文本编辑器不仅需要行内样式和块级样式。例如在Facebook的评论框中，我们使用蓝色背景来高亮提及和话题。

为了支持自定义富文本的灵活性，Draft.js提供了一个`decrator`系统。[Tweet示例](https://github.com/facebook/draft-js/tree/master/examples/draft-0-10-0/tweet)提供了一个实际使用decrator的例子。

## CompositeDecorator

Decorator基于扫描给定[ContentBlock]()的内容，找到满足与定义的策略匹配的文本范围，然后使用指定的React组件呈现它们。

可以使用`CompositeDecorator`类定义所需的装饰器行为。 此类允许你提供多个`DraftDecorator`对象，并依次搜索每个策略的文本块。

Decrator 保存在`EditorState`记录中。当新建一个`EditorState`对象时，例如使用`EditorState.createEmpty()`，可以提供一个decorator。

> ## 幕后
> 在draft编辑器中，当内容发生改变的时候，新的`EditorState`对象会使用decrator重新评估新的`ContentState`标示出要使用decrtor的范围。形成一个包含块级样式、行内样式和decrator的完整状态树，来提供渲染的基础。
>
> 我们通过这样的方式确保当内容改变时，decrator和我们的`EditorState`同步。

在Tweet示例中，我们使用`CompositeDecorator`来查询以@和#开头的字符串：

```javascript
const compositeDecorator = new CompositeDecorator([
  {
    strategy: handleStrategy,
    component: HandleSpan,
  },
  {
    strategy: hashtagStrategy,
    component: HashtagSpan,
  },
]);
```

这个`compositeDecorator`会先扫描@开头的字符串，之后扫描#开头的。

```javascript
// 注意: 这里的正则表达式并没有很严谨，仅作示例!
const HANDLE_REGEX = /\@[\w]+/g;
const HASHTAG_REGEX = /\#[\w\u0590-\u05ff]+/g;

function handleStrategy(contentBlock, callback, contentState) {
  findWithRegex(HANDLE_REGEX, contentBlock, callback);
}

function hashtagStrategy(contentBlock, callback, contentState) {
  findWithRegex(HASHTAG_REGEX, contentBlock, callback);
}

function findWithRegex(regex, contentBlock, callback) {
  const text = contentBlock.getText();
  let matchArr, start;
  while ((matchArr = regex.exec(text)) !== null) {
    start = matchArr.index;
    callback(start, start + matchArr[0].length);
  }
}
```

这里Strategy函数给callback提供了匹配到文本的起始位置和结束位置。

## Decorator的组件

在你要使用Decorator的文本上，你需要提供一个React组件来渲染它们。这里通常使用简单的`span`标签和一些css class来实现。

在我们的例子中，`CompositeDecorator`对象使用`HandleSpan`和`HashtagSpan`这两个组件渲染。它们都是基础的无状态的组件。

```javascript
const HandleSpan = (props) => {
  return <span {...props} style={styles.handle}>{props.children}</span>;
};

const HashtagSpan = (props) => {
  return <span {...props} style={styles.hashtag}>{props.children}</span>;
};
```

注意这里将`props.children`放在组件渲染内容中，这样确保我们要修饰的内容能够被呈现。

### CompositeDecorator之外

提供给EditorState的Decorator对象只需要符合[DraftDecoratorType Flow](https://github.com/facebook/draft-js/blob/master/src/model/decorators/DraftDecoratorType.js)类型定义的期望，这意味着你可以创建任何你想要的Decorator类，只要它们与预期类型相匹配，则不受`CompositeDecorator`约束。

## 设置新的Decorator

此外，还可以在editorState创建之后设置新的Decorator。当然，是通过immutable的方式。

这意味着如果的app在运行中发现你的decorator不能满足需求了或者需要对decorator做一些修改，你可以新建一个decorator对象(或者使用`null`来移除所有decorator)并且使用`EditorState.set()`设置新的decorator。

例如，如果因为某些原因我们想移除编辑器中的@提及，我们可以使用下面的方法：

```javascript
function turnOffHandleDecorations(editorState) {
  const onlyHashtags = new CompositeDecorator([{
    strategy: hashtagStrategy,
    component: HashtagSpan,
  }]);
  return EditorState.set(editorState, {decorator: onlyHashtags});
}
```

这个`editorState`中的`contentState`会被新的decorator重新修饰，并且@提及将在下一次渲染时失效。

再次声明，由于不可变对象在数据上的持久性，上一个状态还是会保存在内存中。