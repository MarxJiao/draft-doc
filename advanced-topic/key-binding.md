# 快捷键绑定

`Editor`组件通过`keyBindingFn`这个prop提供了自定义按键绑定的功能。允许你通过键盘命令来操作你的编辑器。

# 默认快捷键

默认的快捷键函数是`getDefaultKeyBinding`。

由于Draft.js对DOM的渲染和行为的严格控制，基本的快捷键命令需要通过按键绑定系统实现。