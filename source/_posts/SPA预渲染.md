---
title: SPA预渲染
date: 2017-09-05 16:34:49
tags: 
  - "预渲染"
  - "prerender-spa"
categories: "全栈"
---
如果你要预呈现页面来更好地支持 SEO，使用 Webpack 插件：prerender-spa-plugin ，经过测试这是可以在 vue 中使用的。对于经常变动的页面，Prerender.io 和 Netlify 提供预呈现功能从而更好地支持搜索引擎。
# SPA预渲染 —— 使用 prerender-spa-plugin

## 1.安装插件

```
npm install --save-dev prerender-spa-plugin
```
或者
```
yarn add prerender-spa-plugin -D
```

## 2.在 build/webpack.prod.conf.js 文件中里使用

```
// 放在文件顶部
var PrerenderSpaPlugin = require('prerender-spa-plugin')
.
.
.
plugins: [
  new PrerenderSpaPlugin(
    // 要编译的目录
    path.join(__dirname, '../dist'),
    // 你要预呈现的列表
    [ '/' ]
  )
  ...
]
```

如果你想预呈现 /about 和 /contact，那么列表就是 [ '/', '/about', '/contact' ]