# Decorators

我们的富文本编辑器不仅需要行内样式和块级样式。例如在Facebook的评论框中，我们使用蓝色背景来高亮提及和话题。

为了支持自定义富文本的灵活性，Draft.js提供了一个`decrator`系统。[Tweet示例](https://github.com/facebook/draft-js/tree/master/examples/draft-0-10-0/tweet)提供了一个实际使用decrator的例子。

# CompositeDecorator

装饰器概念基于扫描给定[ContentBlock]()的内容，找到满足与定义的策略匹配的文本范围，然后使用指定的React组件呈现它们。

可以使用`CompositeDecorator`类定义所需的装饰器行为。 此类允许您提供多个`DraftDecorator`对象，并依次搜索每个策略的文本块。

Decrator 保存在`EditorState`记录中。当新建一个`EditorState`对象时，例如使用`EditorState.createEmpty()`，可以提供一个decorator。

> ## 幕后
> 在draft编辑器中，当内容发生改变的时候，新的`EditorState`对象会使用decrator重新评估新的`ContentState`标示出要使用decrtor的范围。形成一个包含块级样式、行内样式和decrator的完整状态树，来提供渲染的基础。
>
> 我们通过这样的方式确保当内容改变时，decrator和我们的`EditorState`同步。