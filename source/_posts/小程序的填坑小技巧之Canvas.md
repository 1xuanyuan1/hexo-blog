---
title: 小程序的填坑小技巧之Canvas
date: 2018-05-08 18:03:57
tags:
  - 学习
  - 产出
  - 微信小程序
categories: 学习
---

# 前言

> 小程序分享只能分享到好友或者微信群里，不能分享到朋友圈。

这周接到一个这样的坑爹需求，小程序需要实现一个图片分享的功能。让用户可以把图片发送到朋友圈或者其他渠道。

![](/images/witbox_share.png)

刚开始拿到这个需求，觉得还好，没有太大的感觉, 第一个感觉想把锅甩给后端，调用一个接口让后端传回来一个URL，前端只负责显示就好。^_^ 

当然这么天真的想法基本上不可能实现的，如果你遇到这样的后端，请好好的珍惜他。

言归正传，前端自己做这个功能的话，必须要借助一个东西，那就是 Canvas。

# 初用

Canvas 并不是小程序独有的，html5里也有。不过本人实在是用的不多。唯二两次 其一做h5小游戏的时候 另一次跟本次的需求很接近，也是把一个 div 里的内容放入 canvas 并生成图片 让用户可以保持。

我就按照之前的思路来做是没问题的，小程序自己封装了一套API, 不过 canvas 相关的 API 跟正常 html 的基本一样, 我就按照以前 web 的方式再过一遍。

![](/images/canvas_process.png)

# 开用

首先在我们的wxml写下如下代码

```html
<canvas class="canvas" canvas-id="canvas"></canvas>
```

设置 Canvas 大小，可以直接在 wxml 里的 canvas 标签上设置，也可以用 wcss 。个人比较喜欢用wcss 。

重点来了 `canvas-id` 是小程序里特有的，是canvas 组件的唯一标识符，之后的很多地方都需要用到。

然后我们就开始写 js 了

```JS
// 这里的 canvas 就是之前在 wxml 里的 canvas-id
let ctx = wx.createCanvasContext('canvas')
```

`第一个坑的地方来了，正常来 page 里用是一点没问题没有的，但是在组件里的话，画不了，就是显示不出来。`

查[文档](https://developers.weixin.qq.com/miniprogram/dev/api/canvas/create-canvas-context.html)能发现下列一段话

> 创建 canvas 绘图上下文（指定 canvasId）。在自定义组件下，第二个参数传入组件实例this，以操作组件内 `<canvas/>` 组件Tip: 需要指定 canvasId，该绘图上下文只作用于对应的 `<canvas/>`

很显然，我们就代码改成如下即可

```JS
let ctx = wx.createCanvasContext('canvas', this)
```

# 在相应的位置画文字及图片

具体怎么画，我就不讲了，直接开始讲坑的点。

## 在相应的位置

没错，就一个很坑的点。canvas 是居中的，而且在不同尺寸的屏幕上各个点的位置都不一样。我们需要获取屏幕的宽度以及高度进行计算。（主要是宽度）
小程序提供了如下方面，让我们来获取设备的一些信息
```JS
wx.getSystemInfo({
  success: (res) => {
    this.setData({
      windowWidth: res.windowWidth,
      windowHeight: res.windowHeight,
    })
  }
})
```

## 文字

> 这个是最坑的一个点

看UI给的样式，中间会有一段两行的文字。

开始拿到的时候，觉得so easy。算了每一行的字数，截取两段就不好了嘛。 最后出来的效果确实很坑爹。有长有短，还有超过界限的。

这样明显不能满足我们的需求。而之所以会造成这样的问题。就是因为我们的内容是中英文混合的，中文占两个字符长度而英文只占一个，我们根据字符串的`length`截取时，中英文都是1。
除非是纯英文或者纯中文，才能对齐。怎么解决呢？ 上代码！

```JS
getContent(detail, length = 24, row = 2) {
  let len = 0
  let index = 0
  let content = []
  for (let i = 0; len < detail.length; i++) {
    // 若未定义则致为 ''
    if (!content[index]) content[index] = ''
    content[index] += detail[i]
    // 中文或者数字占两个长度
    if (detail.charCodeAt(i) > 127 || (detail.charCodeAt(i) >= 48 && detail.charCodeAt(i) <= 57)) {
      len += 2;
    } else {
      len++;
    }
    if (len >= length || (row - index == 1 && len >= length - 2)) {
      len = 0
      index++
    }
    if (index === row) break
  }
  return content
}
```

我们可以使用 `charCodeAt` 来获取 charCode，中文的话在 127以上（ps.测试的时候发现 数字也是占两个长度的）。
为了预防之后需求的变动 我把这块提取出来写了一个方法。之后只用传入内容，每行的字数以及行数就好。