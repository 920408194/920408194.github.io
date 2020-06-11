---
title: vue
date: 2020-06-10 20:08:36
categories:
- 前端
tags:
- 前端
- 技术文档
---
本文是我学习VUE时的笔记，大部分内容来自VUE官网。

<!-- more -->

# vue.js
## 1、 why vue

### 1、what‘s vue

- Vue是一套用于构建用户界面的**渐进式框架**，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与[现代化的工具链](https://cn.vuejs.org/v2/guide/single-file-components.html)以及各种[支持类库](https://github.com/vuejs/awesome-vue#libraries--plugins)结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。
- 虚拟DOM

### 

## 2、起步-从html开始

### 1、一个简单的vue.js网站
[页面](../../../../../vue_test.html)

```html
<!-- vue.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <!-- 开发环境版本，包含了有帮助的命令行警告 -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>VUE</title>
</head>
<body>
    

    <!-- html -->
    <div id="app">{{message}}</div>

    <!-- js代码 -->
    <script>

    var app = new Vue({
        el: '#app',
        data: {
            message: 'Hello Vue!'
        }
    })
        
    </script>

</body>
</html>
```

- 现在数据和 DOM 已经被建立了关联，所有东西都是**响应式的**。打开你的浏览器的 JavaScript 控制台 (就在这个页面打开)，并修改 `app.message` 的值，你将看到上例相应地更新。
- 一个 Vue 应用会将其挂载到一个 DOM 元素上 (对于这个例子是 `#app`) 然后对其进行完全控制。那个 HTML 是我们的入口，但其余都会发生在新创建的 Vue 实例内部。

### 2、动态

```html
 <!-- html -->
    <div id="app-2">
        <span v-bind:title="message">
          鼠标悬停几秒钟查看此处动态绑定的提示信息！
        </span>
      </div>

    <!-- js代码 -->
    <script>

    var app2 = new Vue({
        el: '#app-2',
        data: {
            message: '页面加载于 ' + new Date().toLocaleString()
        }
    })
        
    </script>

```

- `v-bind` attribute 被称为**指令**，它们会在渲染的 DOM 上应用特殊的响应式行为。在这里，该指令的意思是：“将这个元素节点的 `title` attribute 和 Vue 实例的 `message` property 保持一致”。如果你再次打开浏览器的 JavaScript 控制台，输入 `app2.message = '新消息'`，就会再一次看到这个绑定了 `title` attribute 的 HTML 已经进行了更新。