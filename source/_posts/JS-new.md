---
title: JS-new
date: 2018-04-03 11:28:11
tags:
  - 学习
categories: 学习
---

JS中并没有类的概念,但是经常性会看到 `var test = new Test('duke')` 这样的写法。很像java里构造实例的方式。

```JS
// ES5
function Test(name) {
  this.name = name
}

// 等价于
function Test(name) {
  var obj = Object.create(Test.prototype)
  obj.name = name
  return obj
}

// ES6
class Test {
  constructor (name) {
    this.name = name
  }
}
```