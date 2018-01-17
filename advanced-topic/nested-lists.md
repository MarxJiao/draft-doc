# 嵌套列表

Draft框架提供了对于嵌套列表的支持。你可以使用`Tab`键和`Shift + Tab`键来增加或者减少列表的缩进深度。

`RichUtils`模块提供了一个`onTab`方法来管理这个行为，应该够大多数使用列表的情况使用了。你可以给`Editer`组件添加`onTab` prop来使用这一功能。

DraftStyleDefault.css提供了一些默认的比较合适的空白距离和列表样式行为。

注意，除了'ordered-list-item' 和 'unordered-list-item' 这两种块级类型，其它情况都不支持空白缩进。
