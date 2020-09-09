---
layout: post
title: vue-devtools
tags: [vue]
---

# [Vue调试神器vue-devtools安装](https://segmentfault.com/a/1190000009682735)

## 前言

vue-devtools是一款基于chrome游览器的插件，用于调试vue应用，这可以极大地提高我们的调试效率。接下来我们就介绍一下vue-devtools的安装。

## chrome商店直接安装

vue-devtools可以从chrome商店直接下载安装，非常简单，这里就不过多介绍了。不过要注意的一点就是，需要翻墙才能下载。

## 手动安装

### 第一步：找到vue-devtools的github项目，并将其clone到本地. [vue-devtools](https://github.com/vuejs/vue-devtools)

```
git clone https://github.com/vuejs/vue-devtools.git
```

### 第二步：安装项目所需要的npm包

```
npm install //如果太慢的话，可以安装一个cnpm, 然后命令换成 cnpm install
```

### 第三步：编译项目文件

```
npm run build
```

### 第四步：添加至chrome游览器

```
游览器输入地址“chrome://extensions/”进入扩展程序页面，点击“加载已解压的扩展程序...”按钮，选择vue-devtools>shells下的chrome文件夹。

/**
*如果看不见“加载已解压的扩展程序...”按钮，则需要勾选“开发者模式”。
*/
```

![img](https://segmentfault.com/img/remote/1460000009682738?w=969&h=345)

![img](https://segmentfault.com/img/remote/1460000009682739?w=738&h=412)

到此添加完成，效果图如下：

![img](https://segmentfault.com/img/remote/1460000009682740?w=958&h=293)

## 结语：vue-devtools如何使用

当我们添加完vue-devtools扩展程序之后，我们在调试vue应用的时候，chrome开发者工具中会看一个vue的一栏，点击之后就可以看见当前页面vue对象的一些信息。vue-devtools使用起来还是比较简单的，上手非常的容易，这里就细讲其使用说明了。

![img](https://segmentfault.com/img/remote/1460000009682741?w=997&h=547)