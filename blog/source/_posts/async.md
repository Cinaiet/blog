---
title: JS异步
date: 2019-12-06 01:10:26
categories: JavaScript
tags: 花满楼
---


**前言**  什么是异步？异步和同步又有什么区别？


通常来说，程序是按顺序执行的，同一时刻只会发生一件事。如果一个函数依赖另一个函数的结果，它那么它只能等待那个函数结束才能继续执行。

----
<!--more-->

### 什么是同步？
一定要等任务执行完了，得到结果，才执行下一个任务。

#### 示例
```
const btn = document.querySelector("button")
  btn.addEventListener("click", () => {
    alert("点击确定之后，后续代码才会执行")
    console.log("aaa")
  })
```

上述代码，一行行按顺序执行。
1. 先获取DOM里的button引用
2. 监听按钮的click事件
  alert弹框出现
  点击弹框确认按钮之后，打印"aaa"

每一个操作在执行的时候，其它任何事件都没有发生，因为JavaScript **任何时候只能做一件事情, 只有一个主线程，其他的事情都阻塞了，直到前面的操作完成。**



----
### 异步
不等任务执行完，直接执行下一个任务。

#### 示例
```
const asyncFunc = function (callback) {
  return setTimeout(() => {
    callback("异步任务执行结束后的结果")
  }, 2000)
}

asyncFunc((result) => {
  console.log(result)
})
console.log("aaa")
```
上述代码，先打印"aaa"，后延时2000毫秒执行callback回调。

#### 什么情况下会用到异步？
当几个任务函数之间没有任何关系，相互之间没有任何影响时，就应该使用异步。

#### 异步代码的本质

通过以下代码示例，来进一步了解异步代码的本质

```
(function() {
    setTimeout(() => {
        console.log(0);
    }); 

    new Promise(resolve => {

        console.log(1);
        
        setTimeout(() => {
            resolve();
            Promise.resolve().then(() => {
                console.log(2);
                setTimeout(() => console.log(3));
                Promise.resolve().then(() => console.log(4));
            });
        });

        Promise.resolve().then(() => console.log(5));

    }).then(() => {

        console.log(6);
        Promise.resolve().then(() => console.log(7));
        setTimeout(() => console.log(8));

    });

    console.log(9);
})();
```
简单版理解
- js 的执行顺序，是先同步再异步。
- 异步中任务的执行顺序: 先微任务 microtask队列，再宏任务macrotask队列
- 调用Promise中的resolve，reject属于微任务队列，setTimeout属于宏任务队列

**同步 > 异步；微任务 > 宏任务**

对以上代码任务执行顺序进行编号
```
(function() {
    setTimeout(() => {
        console.log(0);
    });   // 任务一

    new Promise(resolve => {

        console.log(1);
        // 任务二
        setTimeout(() => {
            resolve();  // 任务三
            Promise.resolve().then(() => {  // 任务四
                console.log(2);
                setTimeout(() => console.log(3)); // 任务八
                Promise.resolve().then(() => console.log(4)); // 任务九
            });
        });  

        Promise.resolve().then(() => console.log(5)); // 任务五

    }).then(() => {

        console.log(6);
        Promise.resolve().then(() => console.log(7));  // 任务六
        setTimeout(() => console.log(8)); // 任务七

    });

    console.log(9);
})();
```

第一步：
任务一的setTimeout 函数属于宏任务，先丢入宏列队。
同步执行，先打印1.同步执行打印 9

| 微任务 | 宏任务
|-----------|------------|
| 任务五 | 任务一  |
|             | 任务二  |

第二步: 
任务二丢入宏列队，执行微任务任务三，打印5
第三步:
同步任务及微任务已经执行完毕，开始执行宏任务
- 执行任务一，打印0
- 执行任务二
```
setTimeout(() => {
            resolve();  // 任务三
            Promise.resolve().then(() => {  // 任务四
                console.log(2);
                setTimeout(() => console.log(3)); // 任务八
                Promise.resolve().then(() => console.log(4));  // 任务九
            });
        });  
```
 

第四步:
 执行任务三，打印 6，将任务六丢入微任务中，任务七丢入宏任务中

第五步:
执行任务四，打印 2，将任务八丢入宏任务，任务九丢入微任务

第六步:
执行任务六，打印7
执行任务九，打印4
任务七，打印8
任务八，打印3



 | 微任务 | 宏任务
|-----------|------------|
| 任务三 |               |
| 任务四 |               |
| 任务六 |  任务七  |
| 任务九 |  任务八  |

最终打印输出的顺序为1，9，5，0，6，2，7，4，8，3


### 小结
异步还是同步执行代码，取决于我们要做什么。




