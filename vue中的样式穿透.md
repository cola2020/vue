---
author: wanfeng
sidebar: 'auto'
date: 2021-8-10

categories: vue
tags:
  - vue
  - Web
---
# vue中的样式穿透(/deep/)

## scoped

### 	1.渲染规则

1. 给 HTML 的 dom 节点添加一个不重复的 data 属性(例如: data-v-5558831a)来唯一标识这个 dom 元素

2. 在每句 css 选择器的末尾(编译后生成的 css 语句)加一个当前组件的 data 属性选择器(例如：[data-v-5558831a] )来私有	化样式
3. vue 项目中存在很多组件，如果在不同组件中编写样式中，使用到同一样式名，则会出现央视冲突的情况，使用`scope`解决

### 	2. 作用

​		scoped 可以实现 style 只作用于当前的 .vue 文件

​		不同组件之间重名的 class 很正常

### 3.演示

​		![演示](https://pic.imgdb.cn/item/611292135132923bf800e89d.png)

可以看到，父元素 ant-modal-root 有哈希值，而子元素 ant-modal-footer 却没有，但是 ant-modal-footer 的两个 button 自元素又有哈希值了

这时候，如果不写 deep，而是让scoped自己来的话

```less
.ant-modal-root {
  .ant-modal-footer {
    border-top: 0;
    overflow: hidden;
    padding: 32px;
    padding-top:18px;
  }
}
```

就会编译出这样的 css 样式：

```less
.ant-modal-root .ant-modal-footer[data-v-5cfc4ef6]
```

但是 ant-modal-footer 是不带哈希值的，所以使用样式穿透，在 ant-modal-footer 前加上

```less
/deep/.ant-modal-footer 
```

然后被编译成

```
.ant-modal-root[data-v-5cfc4ef6] .ant-modal-footer
```

就可以选中 ant-modal-footer

### 4.总结

用到第三方组件的时候，经常会自己定义插槽 slot 来替换原组件里的内容，这时候就很容易遇到要手动用/deep/的问题。遇到这类问题的时候，有时使用 !important ，但并不推荐这样做，况且有时不能生效。这时候可以用 deep 来解决问题。



>文章参考[理解vue中less的scoped和/deep/工作原理 (icode9.com)](https://www.icode9.com/content-4-782920.html)

