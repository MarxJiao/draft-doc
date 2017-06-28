# Entities

这篇文章讨论Enity系统。Draft使用它来标记文本的类型。Entities允许开发者将更加丰富的富文本内容添加到编辑器中。链接、提及和嵌入式的内容都可以使用Enity来实现。

在Draft的代码库中，[link editor](https://github.com/facebook/draft-js/tree/master/examples/draft-0-10-0/link)和[entity demo](https://github.com/facebook/draft-js/tree/master/examples/draft-0-10-0/entity)提供了代码示例，来帮助理解entities的使用，和它们内建的行为。

[Entity API Reference]()提供了一些使用静态方法来创建，修改和更新entity对象的细节。

更多关于最近Entity Api修改的信息和怎样更新你的应用的例子，可以看我们的[v0.10版本迁移指南]()。

# 介绍

一个Enity对象代表Draft编辑器一行文本的元数据。它有3个属性：

* `type`: 指示了entity的类型，例如：'LINK'、'MENTION'、'PHOTO'等。
* `mutability`: 可变性。不要将不可变性和immutable-js混淆，此属性表示在编辑器中编辑文本范围时，使用此Enity对象注释的一系列文本的行为。 这在下面更详细地讨论。
* `data`: 一个包含了一些对于当前enity可选数据的对象。例如，'LINK' enity包含了该链接的href值的数据对象。

所有的entity都存储在ContentState的记录中。这些entity被ContentState和组件中的key引用，用来装饰特定的行。（我们不建议使用以前的api来访问entity，相关内容请参阅issue [#839](https://github.com/facebook/draft-js/issues/839)）。

# 新建和获取entity

可以使用`contentState.createEntity`来创建entity，这个函数接收上边介绍的3个属性为参数。返回一个记录了刚新建的entity的ContentState对象。之后你就能够通过contentState.getLastCreateEntityKey来获取新建的entity的key了。

你可以通过这个key来将entity应用到你的内容中。例如`Modifier`模块包含了一个`applyEntity`方法：

```javascript
const contentState = editorState.getCurrentContent();
const contentStateWithEntity = contentState.createEntity(
  'LINK',
  'MUTABLE',
  {url: 'http://www.zombo.com'}
);
const entityKey = contentStateWithEntity.getLastCreatedEntityKey();
const contentStateWithLink = Modifier.applyEntity(
  contentStateWithEntity,
  selectionState,
  entityKey
);
```

对于给定的文本，你能够在一个`ContentState`对象上，通过`getEntityAt`方法来获取entity key从而传递目标的offset值。

```javascript
const blockWithLinkAtBeginning = contentState.getBlockForKey('...');
const linkKey = blockWithLinkAtBeginning.getEntityAt(0);
const contentState = editorState.getCurrentContent();
const linkInstance = contentState.getEntity(linkKey);
const {url} = linkInstance.getData();
```

# "Mutability"

Entity的Mutability值有三种，分别代表了用户编辑对应内容时的三种表现状态。

请注意，DraftEntityInstance对象始终是不可变记录，此属性仅用于指示注释文本在编辑器中如何“突变”。（未来的变化可能会重命名此属性，以防止命名的潜在混淆。）

### Immutable

如果不移除文本上的entity，文本不能被改变。
