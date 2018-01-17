# v0.10 API迁移指南

Draft.js v0.10版本包含了一个管理`DraftEntity` API的改变。现在不再支持全局的'DraftEntity'模块，而是把`DraftEntity`实例迁移到了`ContentState`中。这意味着`DraftEntity`提供的方法也被迁移到了`ContentState`中。

这个api的改进有很多好处，这些好处将在v0.11版本中获得：

* DraftEntiy实例也是不可变的对象；
* DraftEntity不会被全局访问到；
* 所有entity的改变会触发重新渲染。

## 快速预览

下面列举了有哪些改变，和怎么升级你的应用。

### 新建一个entity

#### old

```javascript
const entityKey = Entity.create(
  urlType,
  'IMMUTABLE',
  {src: urlValue},
);
```

#### new
```javascipt
const contentStateWithEntity = contentState.createEntity(
  urlType,
  'IMMUTABLE',
  {src: urlValue},
);
const entityKey = contentStateWithEntity.getLastCreatedEntityKey();
```

### 获取entity

#### old

```javascipt
const entityInstance = Entity.get(entityKey);
// entityKey是创建entity时生成的字符串
```

#### new

```javascirpt
const entityInstance = contentState.getEntity(entityKey);
```

### Decorator中strategy函数的参数改变

#### old

```javascript
const compositeDecorator = new CompositeDecorator([
  {
    strategy: (contentBlock, callback) => exampleFindTextRange(contentBlock, callback),
    component: ExampleTokenComponent,
  },
]);
```

#### new

```javascript
const compositeDecorator = new CompositeDecorator([
  {
    strategy: (
      contentBlock,
      callback,
      contentState
    ) => exampleFindTextRange(contentBlock, callback, contentState),
    component: ExampleTokenComponent,
  },
]);
```

注意ExampleTokenComponent会传入contentState作为prop。
为什么这样做呢？我们在contentBlock中查找指定的entity时需要contentState，因为entity的api已经迁移到了contentState中：

```javascript
const mutableEntityStrategy = function(contentBlock, callback, contentState) {
  contentBlock.findEntityRanges(
    (character) => {
      const entityKey = character.getEntity();
      if (entityKey === null) {
        return false;
      }
      //要查找到指定的entity或者带有某种属性的entity,
      // 你需要从contentState中把entity取出来
      // 这个例子中我们只获取可编辑的entity 
      return contentState.getEntity(entityKey).getMutability() === 'MUTABLE';
    },
    callback,
  );
};
```

### Decorator中查找entity的strategie

#### old
```javascirpt
function findLinkEntities(contentBlock, callback) {
  contentBlock.findEntityRanges(
    (character) => {
      const entityKey = character.getEntity();
      return (
        entityKey !== null &&
        Entity.get(entityKey).getType() === 'LINK'
      );
    },
    callback,
  );
};
```

#### new

```javascipt
function findLinkEntities(contentBlock, callback, contentState) {
  contentBlock.findEntityRanges(
    (character) => {
      const entityKey = character.getEntity();
      return (
        entityKey !== null &&
        contentState.getEntity(entityKey).getType() === 'LINK'
      );
    },
    callback,
  );
};
```

## 更多信息

更多信息可以查看[升级示例](https://github.com/facebook/draft-js/tree/master/examples/draft-0-10-0)。