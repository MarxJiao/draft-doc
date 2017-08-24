# 自定义Block渲染

这篇文章讨论怎么定制Draft的block渲染。Block渲染用于定义支持的block类型和它们的渲染器，以及将解析之后的的内容转换为已知的Draft block类型。

当解析内容或者使用`convertFromHTML`时，Draft会将标签和Draft block进行匹配，将解析后的内容转化为已知的block渲染类型。

# 默认的block渲染表

| HTML标签 | Draft block类型 |
| ------- | :------------: |
| \<h1/>         | header-one | 
| \<h2/>         | header-two     | 
| \<h3/>         | header-three     |
| \<h4/>         | header-four     |
| \<h5/>         | header-five     |
| \<h6/>         | header-six     |
| \<blockquote/> | blockquote    |
| \<pre/>        | code-block|
| \<figure/>     | atomic|
| \<li/>	     | unordered-list-item,ordered-list-item **|
| \<div/>        | unstyled ***| 

** - 这个Block类型建立在父级元素为\<ul>和\<ol>的基础上

*** - 任何不在上边列表的block类型都会被当做`unstyled`

# 配置block渲染列表

Draft默认的block渲染可以通过给`Editor`组件传入类型为[Immutable Map](http://facebook.github.io/immutable-js/docs/#/Map)的`blockRender` prop来重写。

例如改写默认的block渲染。

```javascript
// 下边的例子中，只允许「heading-two」作为唯一有效的block类型
// 并且将unstyled元素也更新为「h2」
const blockRenderMap = Immutable.Map({
  'header-two': {
    element: 'h2'
  },
  'unstyled': {
    element: 'h2'
  }
});

class RichEditor extends React.Component {
  render() {
    return (
      <Editor
        ...
        blockRenderMap={blockRenderMap}
      />
    );
  }
}
```

除了重写block类型，我们还能够添加自定义的block类型。通过DefaultDraftBlockRenderMap来新建一个blockRenderMap。

扩展默认的block render map。

```javascript
const blockRenderMap = Immutable.Map({
  'section': {
    element: 'section'
  }
});

// 将'section' 作为可用的block类型，并更新unstyled元素
// 同时保持其它的block类型
const extendedBlockRenderMap = Draft.DefaultDraftBlockRenderMap.merge(blockRenderMap);

class RichEditor extends React.Component {
  render() {
    return (
      <Editor
        ...
        blockRenderMap={extendedBlockRenderMap}
      />
    );
  }
}
```

当Draft解析一个已经被解析的html，它将html元素转化为Draft block类型。如果你想让其它的标签也能转化为指定的block类型，你可以在block config中添加`aliasedElements`。

unstyled block类型别名的使用：

```javascript
'unstyled': {
  element: 'div',
  aliasedElements: ['p'],
}
```

# 自定义 block wrappers

默认的情况下，html标签被用来包裹block类型。通过blockRenderMap，React组件也能够用来包裹EditorBlock。

在解析或者使用`convertFromHTML`时html会被扫描，来匹配特定的标签。

