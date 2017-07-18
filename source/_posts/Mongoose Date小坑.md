---
title: Mongoose Date小坑
date: 2017-07-18 17:13:26
tags: 
  - "Mongoose"
  - "踩坑"
categories: "踩坑"
---
# Mongoose Date小坑
今天在用nodejs做后端 mongo做数据库的一个应用时发的一个坑。

``` bash
// 假设有这样一个状态
let where = {
  create_date: {
    $gte: "2017-07-10T16:00:00.000Z",
    $lt: "2017-07-17T16:00:00.000Z"
  }
}
mongoDB.find(where).exec().then(result => {
  console.log(result) // 该处能正常查询获取到数据
})
mongoDB.aggregate().match(where).exec().then(result => {
  console.log(result) // 该处不能正常查询获取到数据
})
```

find时 Date类型的数据可以直接用字符串  match时不行!

# 思考解决
若将其转化为 Date类型是否可行 ？  故做了以下测试

``` bash
let where = {
  create_date: {
    $gte: new Date("2017-07-10T16:00:00.000Z"),
    $lt: new Date("2017-07-17T16:00:00.000Z")
  }
}
mongoDB.find(where).exec().then(result => {
  console.log(result) // 该处能正常查询获取到数据
})
mongoDB.aggregate().match(where).exec().then(result => {
  console.log(result) // 该处能正常查询获取到数据
})
```

OK  fixed! ps.这个算 mongoose的bug吗？ 哈哈😄
