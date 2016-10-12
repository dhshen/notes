#Vue.js

### template v-if
我们可以把一个 <template> 元素当做包装元素，并在上面使用 v-if，最终的渲染结果不会包含它。
```
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```


## 使用笔记
* 在数据完成加载，页面还没有编译之前如何隐藏 Mustache标签`{{ mustache }}`
	使用** v-cloak ** 指令，这个指令保持在元素上直到关联实例结束编译。
	和css规则 `[v-cloak]{display:none;}`配合使用，可以隐藏未编译的 Mustache 标签直到实例准备完毕。
