---
title: Js浮点型运算bug及toFixed
date: 2019-04-25 14:45:26
categories: JavaScript
tags: 花满楼
---


#### 前言
上周上线的一个关于基金类的任务，有一个地方需要对浮点型的数据进行计算转换，漏判了一种情况以及对toFixed的滥用，直接导致线上页面无法正常加载，差点祭天... 

<!--more-->

----


#### 问题复述

服务端返回一个字段a: 0.3456 ，前端要展示a转化成百分比的值 以及 (1 - a) 的百分值，均保留一位小数。

- 行吧，简单。

```
let a = 0.3456
let b = (a * 100).toFixed(1)  // "34.6"
let c = 100 - b // 65.4
```

- 搞定。
- 第一轮测试中期，测试指着某条数据问我，不是保留一位小数莫，这是怎么回事。

```
let a = 0.6923
let b = (a * 100).toFixed(1) // 69.2
let c = 100 - b // 30.799999999999997
```

- 这是因为js浮点型运算有一个bug，众所周知 js 0.3 - 0.1 ≠ 0.2 。

```
subNumber = val => {  // 解决小数运算bug
    let float1 = val.toString().split('.')[1]
    let valLen = float1.length ? float1.length : 0
    let len = Math.pow(10, valLen)
    return (Math.ceil(val * len) / len).toFixed(1)
  }
let a = 0.5923
let b = (a * 100).toFixed(1) // 59.2
let c = subNumber(100 -b) // 40.8
```
- 连续通过了三轮测试，到了生产，出问题了。
- 当a的第三位小数为0 且第四位小数小于5时，按如上方法运行的话，b就是一个没有小数的函数，函数第一行 split('.')[1] 是一个undefined，取undefined的length就会报错，天了噜，上线无bug,自挂东南枝。

- 直接再对c toFixed不就解决了问题莫。但忽略了一个致命的问题，toFixed 是对数值进行四舍五入，前言也说了，这是基金类型的项目，一个百分位可能影响到很多的钱。

-  解决办法的代码就不贴了，思路以及方向已经很清楚了。

##### 总结
- 少用toFixed,能不用就不用。
- 对数组或者字符串的边界值进行判空或做特殊处理的时候需要谨慎。
- 少壮不努力，自挂东南枝。



