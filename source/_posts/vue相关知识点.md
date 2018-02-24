---
title: Vue相关知识点
date: 2018-02-01 12:49:02
tags:
  - 面试
  - 思考
  - 知识点整理
categories: 面试
---

# Vue的整体机制

> 主要流程为 初始化及挂载 => 编译 => 响应式 => Virtual DOM => 更新视图

![](/images/vue-all.png)

## 初始化及挂载

`new Vue()`的时候,Vue会调用一个 `_init` 函数，它会初始化Vue的生命周期、事件、data等，其中最重要的是通过 `Object.defineProperty` 设置 `setter` 与 `getter` 函数，来实现[*响应式*]以及[*依赖收集*]。

初始化之后会调用 `$mount` 函数挂载组件，如果是运行时编译，即不存在 render function 但是存在 template 的情况，需要进行[*编译*]步骤。

## 编译
