---
title: Weex快速开发三端应用：做一个看书软件
date: 2018-03-20 13:34:33
tags:
  - 小册子
categories: 小册子
---
# 小册内容
- [ ] 1.weex 基本原理
- [ ] 2.weex 脚手架的使用
- [ ] 3.weex 的一些基本组件
- [ ] 4.搭建第一个 weex 页面
- [ ] 5.推荐书籍页 & 网络请求
- [ ] 6.我的书架 & 本地缓存
- [ ] 7.书籍目录 & 长列表处理
- [ ] 8.页面之间传值
- [ ] 9.真机运行与调试
- [ ] 10.云打包工具 —— 轻舟
- [ ] 11.小结

# 什么是 Weex ？

> Weex 是一个使用 Web 开发体验来开发高性能原生应用的框架。

Weex 致力于使开发者能基于当代先进的 Web 开发技术，使用同一套代码来构建 Android、iOS 和 Web 应用。具体来讲，在集成了 WeexSDK 之后，你可以使用 JavaScript 和现代流行的前端框架来开发移动应用。

Weex 的结构是解耦的，渲染引擎与语法层是分开的，也不依赖任何特定的前端框架，目前主要支持 Vue.js 和 Rax 这两个前端框架。

![](http://weex-project.io/images/flow.png)

## Vue

Vue (读音 /vjuː/，类似于 view) 是一套用于构建用户界面的渐进式框架。目前主流的三大框架之一，本小册就是用的 Vue 框架开做 Weex 的语法层。详情可参阅[这里](https://cn.vuejs.org/v2/guide/index.html)。

## Rax

Rax 是一个兼容 React 接口的前端框架。详情可参阅[这里](https://alibaba.github.io/rax/)。

# 为什么要学 Weex？

写过H5的同学肯定会被它的简单高效、发布即更新、一条URL可适配多端等这些`快`所吸引，但写过 Native 的同学肯定也会被 Native 的富交互、性能体验、可调用原生能力、可管理内存等特性给我们的业务带来更好的`体验`。

> 快与体验兼得

![](https://mmbiz.qpic.cn/mmbiz_jpg/33P2FdAnjuibdbD0UvQhic1XXu4YibPcqynv7ib4OhfxbMjACDcYH9pmdreJibqpJn65PtQgHwpQPYnYuqslfzSmHVw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1)

# 你会学到什么？

1. weex 基本原理
2. weex 脚手架的使用
3. weex 基本组件的使用
4. weex 的网页调试&真机调试
5. 实战中遇到的一些坑以及相应的解决方案

# 你需要准备什么？

需要你懂一些HTML, CSS 与 JS 的基础，因为本课程是使用 Vue 来做 weex 的语法层，如果你有一定的 Vue 基础，你将更好的学习。

- 一台移动设备（Android或者iOS手机）
- 一台可以联网的电脑 
- 还需要一点点的前端基础

# 购买须知

1. 本小册为图文形式内容服务，共计 11 节；
2. 购买用户可享有小册永久的阅读权限；
3. 购买用户可进入小册微信群，与作者互动；
4. 掘金小册为虚拟内容服务，一经购买成功概不退款；
5. 掘金小册版权归北京北比信息技术有限公司所有，任何机构、媒体、网站或个人未经本网协议授权不得转载、链接、转贴或以其他方式复制发布/发表，违者将依法追究责任；
6. 在掘金小册阅读过程中，如有任何问题，请邮件联系 xiaoce@xitu.io