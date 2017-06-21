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