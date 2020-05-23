## 关于 scoped 的一些知识点

在 Vue 单文件组件 style 中，可以通过添加 scoped 属性来定义组件私有样式，该样式仅对组件本身有效，不会对全局样式造成污染，形成了样式的“作用域”概念。

那 Vue 是如何实现 scoped 私有样式，关于 scoped 样式有哪些需要注意的地方呢？

### 工作原理

首先我们以一个简单的 Header 组件为例来讲解 scoped 的工作原理，以下代码定义了一个 Header 组件，

```js
<template>
    <div>
        <h1>我是Header组件</h1>
    </div>
</template>
<script>
export default {
    name: "Header",
    data() {
        return {};
    }
};
</script>
// 这里添加scoped属性，定义组件内部私有样式
<style scoped>
h1 {
    text-align: center;
    border: solid 2px cyan;
    margin: 0;
    padding: 2rem;
    background-color: #eee;
}
</style>
```

组件定义好之后，我们在父页面引入，项目运行后可以看到如下效果：

![](https://cdn.nlark.com/yuque/0/2020/png/1076531/1586441681415-b1ac7fdd-d27a-4bf7-9027-7e90ca83e9cc.png)

F12 打开浏览器控制台，关注如下代码段，我们可以发现组件模板内的每个标签都被添加了一个`data-v-hash`的属性，且样式表中的标签选择器 h1 后同样被追加了属性`data-v-hash`，在这种情况下，如果我们在组件外使用 h1 标签，组件内的 h1 选择器（有`data-v-hash`属性）是无法选择到外层 h1 标签（无属性）的，也即组件内外的 h1 样式实现了分开定义，从而达到了组件样式私有化的目的。

![](https://cdn.nlark.com/yuque/0/2020/png/1076531/1586441418835-64c3ae82-40f5-4baf-919e-a49ad1596d2c.png)

> Vue 使用 PostCss 对使用了 scoped 的样式进行转换，并添加 data-v-hash 属性

### 注意事项

父子组件均使用 scoped 属性时，无法在父组件内直接修改子组件内部样式

我们在父组件中引入 Header 组件，同时父组件中 style 添加 scoped 属性，并定义 h1 的字体颜色为绿色：

```css
<style scoped>
.home h1 {
    color: green;
}
</style>
```

父组件添加该样式后，我们可以看到页面效果如下：

![](https://cdn.nlark.com/yuque/0/2020/png/1076531/1586446080822-da24d4d3-c167-4f88-8b25-a42f0ec4a21b.png)

父组件的 h1 颜色为绿色，这个符合预期，但是子组件的 h1 字体颜色没有按照我们的样式定义进行更新，打开控制台查看代码可以发现，由于父子组件均使用 scoped，组件内的 h1 样式均添加了 data-v-hash 属性，但属性值不同，因此父组件内的样式定义对子组件内部不起作用。

![](https://cdn.nlark.com/yuque/0/2020/png/1076531/1586446487283-77110ce9-0544-4b4f-9e68-f966fb02155e.png)

解决方案：

1. 删除父组件的 scoped 属性，这样父组件的 h1 能直接选中子组件内部的 h1（注意权重）；
2. 另外添加一个 style 样式（无 scoped），相关样式在这个 style 标签下定义，Vue 允许我们在一个组件内同时使用 scoped 和无 scoped 样式；
3. 使用 >>> 或 /deep/ 深度操作符（Sass、Less 等预处理器无法正确解析 >>>，可以使用 /deep/ ）

需要注意，方案 1，2 可能会污染全局样式（可通过在最外层增加 id 或 class 避免），应该在清楚相关影响的情况下使用；这里我们使用第三种方案进行演示，页面效果如下，满足我们的需求。

```css
<style scoped>
.home /deep/ h1 {
    color: green;
}
</style>
```

![](https://cdn.nlark.com/yuque/0/2020/png/1076531/1586447525366-77335801-39aa-487c-bede-67e33ff5c0a7.png)

查看控制台样式代码发现，深度操作符>>>，/deep/将 data-v-hash 放在了父组件的类名后，因此能正常选中子组件内部的 h1 标签。

![](https://cdn.nlark.com/yuque/0/2020/png/1076531/1586448065748-15d8b66f-537c-4f28-b1eb-7befafe26726.png)

以上就是关于 scoped 的一些知识点，了解这些能帮助我们实现符合预期的页面/组件样式。
