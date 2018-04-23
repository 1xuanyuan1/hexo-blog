---
title: JavaScript设计模式阅读笔记
date: 2018-04-03 12:00:33
tags:
  - 读书笔记
  - 学习
categories: 学习
---

# 前言

遇到一个问题，给别人解释一个东西,经常要讲一堆，讲完之后得到一个回复，那不是就是xx模式吗。

“模式”取名的意义非常重要。人类可以走到生物链顶端的前两个原因分别是会“使 用名字”和“使用工具”。在软件设计中，一个好的设计方案有了名字之后，才能被更好地传播， 人们才有更多的机会去分享和学习它们。
也许这个小故事可以说明名字对于模式的重要性:假设你是一名足球教练，正在球场边指挥 一场足球赛。通过一段时间的观察后，发现对方的后卫技术精湛，身体强壮，但边后卫速度较慢， 中后卫身高和头球都非常一般。于是你在场边大声指挥队员:“用速度突破对方边后卫之后，往 球门方向踢出高球，中路接应队员抢点头球攻门。”
在机会稍纵即逝的足球场上，教练这样费尽口舌地指挥队员比赛无疑是荒谬的。实际上这种 战术有一个名字叫作“下底传中”。正因为战术有了对应的名字，在球场上教练可以很方便地和 球员交流。“下底传中”这种战术即是足球场上的一种“模式”。

`设计模式的主题总是把不变的事物和变化的事物分离开来`
>`不同的设计模式就是在不同的场景下实现把不变的事物和变化的事物分离开来的解决方案总结`

## 基础知识学习

发现两个好玩的方法

### includes 是否包含
`Array.prototype.includes()`, `String.prototype.includes()` 分别在 ES2016 与 ES2015 中支持，部分浏览器有兼容问题

```JS
let arr = [1,2,3,4,'5']
console.log(arr.includes('5')) // true
let str = 'hello world'
console.log(str.includes('world')) // true
```

### in 指定的属性是否在指定的对象或其原型链中

```JS
let person = { name: 'duke' }
let boy = Object.create(person)
boy.age = '18'
console.log('age' in boy) //  true; boy 对象本身的属性
console.log('name' in boy) // true; boy 原型链上父类 person的属性
console.log('sex' in boy) // false; 在boy以及其原型链上并没有 sex
```

## 闭包
闭包是JS的一大重点，主要有以下作用

1. 封装变量
2. 延续局部变量的寿命

## 高阶函数

1. AOP

> AOP(面向切面编程)的主要作用是把一些跟核心业务逻辑模块无关的功能抽离出来，这些 跟业务逻辑无关的功能通常包括日志统计、安全控制、异常处理等。把这些功能抽离出来之后， 再通过“动态织入”的方式掺入业务逻辑模块中。这样做的好处首先是可以保持业务逻辑模块的纯净和高内聚性，其次是可以很方便地复用日志统计等功能模块。

2. currying & uncurrying

> currying 又称部分求值。一个 currying 的函数首先会接受一些参数，接受了这些参数之后， 该函数并不会立即求值，而是继续返回另外一个函数，刚才传入的参数在函数形成的闭包中被保 存起来。待到函数被真正需要求值的时候，之前传入的所有参数都会被一次性用于求值。

3. 函数节流

> JavaScript 中的函数大多数情况下都是由用户主动调用触发的，除非是函数本身的实现不合 理，否则我们一般不会遇到跟性能相关的问题。但在一些少数情况下，函数的触发不是由用户直接控制的。在这些场景下，函数有可能被非常频繁地调用，而造成大的性能问题。
`window.onresize 事件`、`mousemove 事件`、`上传进度` ...

- 频繁调用的函数，设置一个setTimeout延迟一段时间执行，若上一个延迟执行还没有结束则忽略接下来该函数的调用。

4. 分时函数

> 某些函数确实是用户主动调用的，但因为一些客观的原因，这些函数会严 重地影响页面性能。比如直接渲染一个1000+条长列表时，可能会造成页面卡顿甚至未响应。

5. 惰性加载函数

## 单一职责原则


# 设计模式

## 单例模式

> 单例模式的定义是:保证一个类仅有一个实例，并提供一个访问它的全局访问点。

```JS
var createLoginLayer = (function(){
  var div;
  return function(){
    if (!div){
      div = document.createElement( 'div' );
      div.innerHTML = '我是登录浮窗';
      div.style.display = 'none';
      document.body.appendChild( div );
    }
    return div;
  }
})();
console.log(createLoginLayer() === createLoginLayer()) // true
```

> 上述方法的确可以创建一个单例，但是其实际上包含了两个职责，`管理单例`与`创建对象`。如果我们仅需要`创建对象`或者要复用`管理单例`的话，其实是做不到的或者就需要全部重新写。根据单一职责原则我们可以把上述代码进行如下拆分。

```JS
// 将普通实例方法转化为单例
var getSingle = function( fn ){
  var result;
  return function(){
    return result || ( result = fn.apply(this, arguments) );
  }
};
// 创建登录界面
var createLoginLayer = function(){
  var div = document.createElement( 'div' );
  div.innerHTML = '我是登录浮窗';
  div.style.display = 'none';
  document.body.appendChild( div );
  return div;
};
// 创建登录界面单例
var createSingleLoginLayer = getSingle( createLoginLayer );
console.log(createSingleLoginLayer() === createSingleLoginLayer()) // true

```

## 策略模式

> 策略模式的定义是:定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换。

### 计算年终奖金

```JS
var calculateBonus = function( performanceLevel, salary ){
  if ( performanceLevel === 'S' ){
    return salary * 4;
  }
  if ( performanceLevel === 'A' ){
    return salary * 3;
  }
  if ( performanceLevel === 'B' ){
    return salary * 2;
  }
};
calculateBonus( 'B', 20000 ); // 输出:40000
calculateBonus( 'S', 6000 ); // 输出:24000
```

可以显而易见发现一些缺点

- calculateBonus类很复杂，包含了很多的`if-else`
- calculateBonus缺乏弹性，若要增加一个C类或者修改S类的系数，则需要修改calculateBonus函数的内部实现，违反了`开发-封闭`原则。
- 复用性太长，若在其他地方有用到这些奖金算法,只能复制黏贴过去。

```JS
var strategies = {
  "S": function( salary ){
    return salary * 4;
  },
  "A": function( salary ){
    return salary * 3;
  },
  "B": function( salary ){
    return salary * 2;
  }
};
var calculateBonus = function( level, salary ){
  return strategies[ level ]( salary );
};
console.log( calculateBonus( 'S', 20000 ) ); // 输出:80000
console.log( calculateBonus( 'A', 10000 ) ); // 输出:30000
```

### 表单验证

```JS

```