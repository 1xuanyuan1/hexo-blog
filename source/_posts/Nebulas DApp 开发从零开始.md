---
title: 人生的第一桶金 —— Nebulas DApp 开发从零开始
date: 2018-05-14 11:23:42
tags:
  - 区块链
  - DApp
  - Nebulas
categories: 全栈
---
# 前言
5月7日 00:00 - 7月2日 00:00，Nebulas(星云) 有一个激励计划，开发DApp, 最高月度优秀奖励可达 `20000 NAS 价值约 120w RMB`

人生的第一个一百万，第一桶金，兴不兴奋， 心不心动！！~  不管你心不心动, 反正我心动了！

第一步肯定是先注册一个账号了， [注册链接](https://incentive.nebulas.io/cn/signup.html?invite=pWryl)

好了，看到钱就兴奋不算一件坏事，不过还需要冷静下，这120w 不是这么好拿的，不过对于普通的开发者来说 还有一个很大的福利, 只要你成功上架一个DApp,即可获得 `100 NAS 价值 6000 RMB` 这都我等来说 也算一笔不大不小的收入, 如果写的优秀点的话，还有更多的奖项在等着，就算拿不到 `120w`，努力点 那个几万 甚至十几 二十万 还是有可能的。具体可以查看[官网说明](https://nebulas.io/cn/incentive.html)。

ps. 当然官方对帮忙推广宣传的，也有一定的奖励，如果小伙伴们通过我上面的邀请链接进入的，并且成功上架一个DApp的话 我也是能获取到 `40 NAS`，当然你自己本身的奖励不会减少（据社区小伙伴透露，通过别人邀请链接进入的，成功上架一个DApp 的奖励上升到`110 NAS`）

# 准备工作

前言打了一波鸡血, `120w` 想拿到，怎么说都要成功上架一个 DApp之后，才有这个机会。讲一下准备工作吧。

1. 首先你得会 JavaScript(TypeScript 也行)，不会的小伙伴 自行学习下。
2. 其次你得了解下 DApp 是个什么东西？
3. 开发流程是怎么样的？怎么部署以及发布？
4. 去哪里提交上架？

# DApp 是个什么东西？

其实建议自行 google 百度下，会清楚点，在这里我做个简单的介绍，自己的理解，可能有所出处。

DApp 即 decentralized application (去中心化应用)，主要是相当于中心化应用来说的。目前的App来说，基本上都是中心化应用，有中心化的服务器，数据存储读取都是跟中心化服务器做交互。而去中心化应用，去掉的就是中心化的服务器，才用的是区块链的去中心化的数据库。

最早应该是以太坊提出智能合约之后才出来的一个概念，至于`智能合约`，个人理解是相当于后端，数据通过智能合约存储在区块链上。

# 开发流程

重头戏来了，基友星云链开发一个DApp的流程是怎么样的

1. 先去星云官网注册账号，[注册链接](https://incentive.nebulas.io/cn/signup.html?invite=pWryl)
2. [下载官方web钱包](https://github.com/nebulasio/web-wallet), 并使用该web钱包创建我们自己的钱包
3. [下载官方Chrome插件](https://github.com/ChengOrangeJu/WebExtensionWallet)，github上有教程
4. 开发智能合约，（本文会采用官方推荐的最简单的一个例子）
5. 部署智能合约，（部署合约的时候需要少量的NAS, 测试链上的币可以在[这里](https://testnet.nebulas.io/claim)领取,正式链上的NAS,如果没有渠道的话，可以联系我微信 `xuanyuan_1`, 备注请按 `掘金-DApp-你的名字`, 如果你是按我的邀请链接注册的我将免费送你一点NAS币 用于部署智能合约。）
6. 开发Web前端界面
7. 部署前端界面（这块不熟悉的，或者没有域名的，也可以联系我）
8. 提交DApp（坐等拿钱）

ps. 拿到手的是NAS，如果不想持有直接提现的话，可以去火币等交易所提现

本文会主要讲一下智能合约，以及web前端开发两个部分。

## 智能合约

如果没有智能合约这个概念的话，可以把这块理解后端的接口。星云的智能合约是基于 Chrome 的 V8 引擎开发的，支持 `JavaScript` 与 `TypeScript`。
智能合约的执行模型如下：
![](https://github.com/nebulasio/wiki/raw/master/resources/smart_contract_execution_model.png)
内置了一下 `LocalContractStorage` 模板来提供存储功能，`BigNumber` 模块进行数值的处理，`Blockchain` 对象可以取得当前合约所在的块和Transaction信息... 具体可以参考[官方的文档](https://github.com/nebulasio/wiki/blob/master/smart_contract_ch.md)

### 写智能合约

直接上代码

```JS
"use strict";

var DictItem = function(text) {
  if (text) {
    var obj = JSON.parse(text);
    this.key = obj.key;
    this.value = obj.value;
    this.author = obj.author;
  } else {
    this.key = "";
    this.author = "";
    this.value = "";
  }
};

DictItem.prototype = {
  toString: function () {
    return JSON.stringify(this);
  }
};

var SuperDictionary = function () {
  LocalContractStorage.defineMapProperty(this, "repo", {
    parse: function (text) {
      return new DictItem(text);
    },
    stringify: function (o) {
      return o.toString();
    }
  });
};

SuperDictionary.prototype = {
  init: function () {
    // todo
  },

  save: function (key, value) {
    key = key.trim();
    value = value.trim();
    if (key === "" || value === ""){
        throw new Error("empty key / value");
    }
    if (value.length > 64 || key.length > 64){
        throw new Error("key / value exceed limit length")
    }

    var from = Blockchain.transaction.from;
    var dictItem = this.repo.get(key);
    if (dictItem){
        throw new Error("value has been occupied");
    }

    dictItem = new DictItem();
    dictItem.author = from;
    dictItem.key = key;
    dictItem.value = value;

    this.repo.put(key, dictItem);
  },

  get: function (key) {
    key = key.trim();
    if ( key === "" ) {
      throw new Error("empty key")
    }
    return this.repo.get(key);
  }
};
module.exports = SuperDictionary;
```

上面的智能合约就是就是官方demo里的，吐槽一下写这个demo的人 可能是之前写 java 的或者是强行把 TypeScript 撸成 JavaScript。

完成可以改写成下方形式, （为了节省篇幅把说明直接写在注释里了）

```JS
// 构建一个SuperDictionary 类 ES5的写法 可以用ES6 class的写法也行
var SuperDictionary = function () {
  LocalContractStorage.defineMapProperty(this, "repo");
};

SuperDictionary.prototype = {
  // 这个方法比较有，初始化合约的时候 做相应的处理， 比如说 初始化的时候 绑定直接是用户
  init: function () {
    // todo
  },
  /**
   * 保存内容 
   * key: 关键字
   * vlaue: 关键字所对应的定义
   */
  save: function (key, value) {
    key = key.trim();
    value = value.trim();
    if (key === "" || value === ""){
        throw new Error("empty key / value");
    }
    if (value.length > 64 || key.length > 64){
        throw new Error("key / value exceed limit length")
    }
    /**
     * Blockchain.transaction.from 即本次请求的区块链地址
     */
    var from = Blockchain.transaction.from;
    var dictItem = this.repo.get(key);
    // 若已经存在 则这个词已经被抢注了 抛出异常
    if (dictItem){
        throw new Error("value has been occupied");
    }
    // 很自信的觉得 下面的写法是对的 没想到测试没通过 可怕
    // dictItem = {
    //   author: from
    //   key,
    //   value
    // }
    // 最后改成如下写法
    dictItem = {};
    dictItem.author = from;
    dictItem.key = key;
    dictItem.value = value;
    // 保存
    this.repo.put(key, dictItem);
  },
  // 获取相关保存的词
  get: function (key) {
    key = key.trim();
    if ( key === "" ) {
      throw new Error("empty key")
    }
    return this.repo.get(key);
  }
};
module.exports = SuperDictionary;
```

这个DApp要实现的什么功能，其实看合约 就知道了。这是一个名为 SuperDictionary 的DApp, 主要实现的功能即为可以在这个 DApp 查询一个词，若这个词没有被人抢注，你就可以自己注册下（比如说 搜我的大名 `Duke` 基本上不会有人注册，我给它一个定义 `世上最帅的人`，那么这条信息就将永久保存在星云链上，别人再使用这个DApp 查询 Duke 可以看到 我给他下的定义。）

星云链的智能合约有三点简要的规范：

1. 智能合约代码必须是一个Prototype的对象；
2. 智能合约代码必须有一个init()的方法，这个方法只会在部署的时候被执行一次；
3. 智能合约里面的私有方法是以_开头的方法，私有方法不能被外部直接调用；

### 部署智能合约

部署智能合约有三步

1. 检查你的账户的状态
2. 若账户里有足够的钱来支付手续费则开始解锁账户（解锁时间为12小时）
3. 再解锁的期间 发送部署BankVault合约的交易

详情可查看[这里](https://github.com/nebulasio/wiki/blob/master/tutorials/%5B%E4%B8%AD%E6%96%87%5D%20Nebulas%20101%20-%2003%20%E7%BC%96%E5%86%99%E6%99%BA%E8%83%BD%E5%90%88%E7%BA%A6.md#%E9%83%A8%E7%BD%B2%E6%99%BA%E8%83%BD%E5%90%88%E7%BA%A6)

这三步都需要发一个请求出去，而且例子上的地址是 `localhost` 意味着 你还需要编译安装一个星云链的代码，相当于成为星云链的一个节点。
so trouble!!! 我只是想安静的撸一个 DApp， 这些是什么鬼！！！

别担心我有招（其实官方也考虑到这点了）

到官方github 把 [web版钱包](https://github.com/nebulasio/web-wallet) 下载下来，`npm install` 安装依赖，再 `node server.js` 运行服务。再接下来在浏览器中输入 `http://localhost:8080/` 即可看到如下界面

![](/images/nebulas_novice_img1.png)

![](/images/nebulas_novice_img2.png)

这样我们就部署成功了，到此为止我们就基本上完成了，DApp后端部分的工作，我们可以开始愉快的撸前端界面了。

> ps. 开发的时候，我们最好用的是测试链，测试链上的币可以在[这里](https://testnet.nebulas.io/claim)领取，但是提交

## 前端界面

基本上的界面，大家就自我发挥吧。主要来讲一下会遇到的坑点。怎么调用

官方提供了一个方法 [nebPay](https://github.com/nebulasio/nebPay), 但是官方并没有把他提交到npm上去，有点忧伤，只能自己动手 占个坑， 直接可以使用 `npm install nebpay` 就可以下载

> 最开始自己傻逼了，以为只能上传到npm上才能用，后来发现在 package.json 直接写 `"${name}":"git+${it's github url}"`也行，拿nebPay举例即为`"nebpay": "git+https://github.com/nebulasio/nebPay"`。

### api 调用

前面讲了前端调用合约的方式，具体代码怎么写呢,我简单的封装了下，大家可以做个参考

```JS
import NebPay from 'nebpay'
const nebPay = new NebPay()
/**
 *
 * @param {String} callFunction 调用合约的方法
 * @param {Array} param 请求的参数
 * @param {Boolean} needPay 是否需要支付
 */
const api = function (callFunction, param = [], needPay = false) {
  return new Promise((resolve, reject) => {
    const to = dappAddress
    const value = '0'
    const callArgs = JSON.stringify(param)
    /**
     * 如果只查询类的请求 就不需要去支付 直接使用 nebPay 的 simulateCall 即可
     * 如果不是查询类的 需要调用支付接口 使用 nebPay 的 call 调用
     */
    const way = needPay ? 'call' : 'simulateCall'
    nebPay[way](to, value, callFunction, callArgs, {
      listener: (res) => {
        console.log('listener', res)
        try {
          resolve(JSON.parse(res.result))
        } catch (e) {
          reject(res.result)
        }
      }
    })
  })
}
```

显而易见，只要用到了两个方法，`call` 与 `simulateCall`, 两者的在于是否需要修改合约里的内容。比如查询类的就想我们的合约里的 get 方法，我们就用 simulateCall， 但是如我们合约里的 save 方法就需要使用 call，会调用 nebulas 的 chrome 钱包插件，需要支付少量的矿工费，这样我们才能把我们的信息永久保存在星云链上。

# 结语

一不小心啰啰嗦嗦了，写了一堆，不知道对小伙伴们，有没有帮助。今天（2018-05-21）已经是激励计划的第三周了，基本上简单的应用都被人占坑了，如果想要过审核，需要多一点点创意，多一点点功能把 DApp 做的稍微饱满点, 单人闭门造车已经很难了，有兴趣的或者有好想法的小伙伴，一起组个队一起玩，后续的机会还是有的。真获奖了奖励平分。 我微信及手机号为： MTg5MDY3Nzc1NzM= （加微信就好 ^_^ 请备注 掘金）