---
title: 为SimpleReact添加声明周期
categories:
  - JavaScript
tags:
  - 核心原理
toc: false
date: 2021-08-01 17:24:55
---

在[上一篇](https://blog.wangguanwei.com/2021/07/25/JSX%E7%9A%84%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86/)文章中，初步的实现了**自定义组件**，本文将补全React的State和生命周期。

<!--more-->

在React中，我们可以认为它是由一个模板加上一个数据，来组成的我们UI。通过对数据的更改，重新使用render函数来实现UI的更改。

`Ps: 气死我了，写到11点多草稿栏里没保存...`

## state(贫血模型)

其实state说起来就是一个非常简单的东西,就是一个普通的js对象，对象中存了一些数据,而这个对象中，不存在一些复杂的数据结构，如函数等..

### setState

setState 不但需要更改state的值本身之外，还需要启动重新render的动作。当多次setState时，它会在整个生命周期结束之后，发起一次重新的render。

**一个组件要想知道何时更新，一定是在render的环节中去得知的。**

在render函数中取root的过程，实际上就是一个真实的渲染过程。

## range

在`Component`函数，`get root`函数中，如果render回来的结构是`Component 子类`,那么就会对this.root循环调用最终得到elementWraapper 或textWrapper的实例。

所以this.root 是与渲染所关联的关键，想要实现更新，需要添加一个私有函数。

### 私有函数

每次把`Component`创建出来，在render前，都需要调用到它的具体位置，而此时若是传入一个element，位置是不够精确的，因为我们可能在两个element中间，做重新渲染的话，dom不一定是要插入到最后的。

DOM API 中有一个 [Range API](https://developer.mozilla.org/zh-CN/docs/Web/API/Range) 可以精准的获取到对应的位置。

此时应由取一个元素，变化为渲染进一个range里。

- 定义一个私有变量用于class中声明私有函数用于替换掉get root
- elemenetWrapper and textWrapper 增加私有函数，render range
  
  ```
  // main.js
  
    const REDDER_TO_DOM = Symbol('render to dom')
  
    // class Component
    [REDDER_TO_DOM](range) { // 私有函数
        // Range 接口表示一个包含节点与文本节点的一部分的文档片段。可将文本插入到指定位置
  
        this.render()[REDDER_TO_DOM](range)
      }
  ```
- elementWrapper appendChild 将range插入到指定位置
  
  ```
  // class elementWrapper and textWrapper add 
  
  [REDDER_TO_DOM](range) { // 私有函数
      range.deleteContents()
      range.insertNode(this.root)
    }
  
    // class elementWrapper 
  
    appendChild(component) {
      let range = document.createRange()
        range.setStart(this.root, this.root.childNodes.length)
        range.setEnd(this.root, this.root.childNodes.length)
        range.deleteContents()
        component[REDDER_TO_DOM](range)
    }
  ```
- 更改render函数
  
  ```
  export function render(component, parentEle) {
    let range = document.createRange()
    range.setStart(parentEle, 0)
    range.setEnd(parentEle, parentEle.childNodes.length)
    range.deleteContents()
    component[REDDER_TO_DOM](range)
  }
  ```

## 重新绘制

### rerender

- 保存range 用于重新绘制 this._range = range
- 从文档中移除range包含的内容
- 重新调用私有函数传入this._range
- 在Component 子类绑定事件中调用this.rerendeer触发重新调用

```
// class Component
[REDDER_TO_DOM](range) { // 私有函数
    // Range 接口表示一个包含节点与文本节点的一部分的文档片段。可将文本插入到指定位置
    this._range = range
    this.render()[REDDER_TO_DOM](range)
  }

  rerender() {
    this._range.deleteContents()
    this[REDDER_TO_DOM](this._range)
  }
```

### 添加事件绑定

添加一个 this.state + 1 的onClick事件

```
<button onclick={() => {this.state.a++; this.rerender()}}>add a ++</button>s
```

### addEventListener

由于当前的`element setAttribute`并没有对事件进行监听添加，所以需要对以on开头的进一步处理

```
setAttribute(name, value) {
    if(name.match(/^on([\s\S]+)/)) {
      this.root.addEventListener(RegExp.$1.replace(/^[/s/S]/, val => val.toLowerCase()), value) // 转为小写
    } else {
      this.root.setAttribute(name, value)
    }
  }
```

在真正的React中，this.state change and rerender 的触发实际上是一个合成语句(setState)

## setState

### 优点

- setState 可以实现对象的合并
- rerender也不需要再手动的去调用。

### 编写setState

众所周知，React 数据更新是采用替换的形式去更新的。

- 首先判断当前this.state 是否是一个对象 ?
- case false: this.state = newState && rerender() && return
- caase true: 递归遍历newState对象中的item是否存在于oldState?
- step3 case false: oldState[item] = newState[item]
- setp3 ccase true : 递归调用传入oldState[item] && newState[item]
- this.render()

```
setState(newState) {
    if(this.state === null || typeof this.state !== 'object') {
      // 不是一个对象时
      this.state = newState
      this.rerender()
      return 
    }
    let merge = (oldState, newState) => {
      for(let item in newState) {
        if(oldState[item] === null || typeof oldState[item] !== 'object') {
          oldState[item] = newState[item]
        } else {
          merge(oldState[item], newState[item])
        }
      }
      
    }

    merge(this.state, newState)
    this.rerender()
  }

}
```

修改`MyComponent`绑定的事件：

```
<button onclick={() => {this.setState({a: this.state.a + 1})} }>add a ++</button>
```

至此就完成了setState 函数的编写。

未完待续...

