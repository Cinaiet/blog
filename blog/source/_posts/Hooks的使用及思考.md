---
title: Hooks的使用及思考
date: 2022-11-03 17:00:54
categories: JavaScript
tags:
  - 花满楼
  - 重学React
---

## 为什么要用 Hooks

其实在类组件时代时，已经有了函数组件的雏形。但是有一个局限，就是函数组件无法存在内部状态，必须是纯函数，而且无法提供完整的生命周期。**故使用了 Class 作为组件。**

---

<!--more-->

### 使用 Class 组件的缺点

- **没有用到 Class 的继承特性。**当使用 class 组件作为载体时，会发现**React 组件之间是不会互相继承的。** 如创建了一个 Button 组件,再创建组件 DropdownButton 时不会继承 Button。
- **因为所有 UI 都是由状态驱动的，因此很少会在外部去调用一个类的实例方法。** 因为 Class 组件的所有方法都是在内部函数或者被作为生命周期调用的。

### Hooks 的诞生

函数和对象不同，函数不能够在多次执行之间还能保存状态，所以就需要一个函数之外的空间来保存这个状态（如 15.x 版本时使用 Class），并且要能检测其状态的变化，从而能触发函数组件的重新渲染。

也就是说需要一个机制，把外部的数据绑定到函数的执行。当数据变化时，函数能够重新自动执行。

这个机制，就是 Hooks.

Hook 就是"钩子"的意思。在 React 中，Hooks 就是**把某个目标结果钩到某个可能会变化的数据源或事件源上，当数据或事件发生变化时，产生这个目标结果的代码会重新执行，产生更新。**

### Hooks 优点

- 简化逻辑复用
  如根据浏览器的宽度，重新调整布局。

```
const getSize = () => {
  return window.innerWidth > 1000 ? "large" : "small";
}
const useWindowSize = () => {
  const [size, setSize] = useState(getSize());
  useEffect(() => {
  const handler = () => {
      setSize(getSize())
    };
    window.addEventListener('resize', handler);
    return () => {
      window.removeEventListener('resize', handler);
    };
  }, []);

  return size;
};


const Demo = () => {
  const size = useWindowSize();
  if (size === "small") return <SmallComponent />;
  else return <LargeComponent />;
};

```

而在类组件中，我们就需要实现一个高阶函数去判断 size 值，然后走不同的业务逻辑。

- 有助于关注分离

## useState

useState 让函数组件具有维持状态的能力。

1. useState(params) 的参数 params 是创建 state 时的初始值，可以是任意类型，bool,string,number, obj 等。
2. useState()的返回值，是有着两个元素的数组，第一个用于读取当前定义的 state 的值，第二个用来设置当前 state 的值。
3. 如果要创建多个 state，则需要多次调用 useState。

```
const [loading, setLoading] = useState(false)
const [age, setAge] = useState('18')
const [arr, setArr] = useState([{temp： 1}])

```

### state 的最佳实践

**state 不要保存可以通过计算得到的值。一旦组件有自己状态，意味着组件如果重新创建，就需要有恢复状态的过程，这通常会让组件变得更复杂。**

- 从 URL 中获取到的值，不建议读取出来之后放 state 里。
- 从 cookie、localStorage 等获取到的值，建议每次使用时直接读取，不建议放入 state 里。
- 从 props 传递过来的值，不能直接使用，需要经过计算或转换之后才能在 UI 上展示，不建议直接放到 state 里。

## useEffect 执行副作用

useEffect(callback, dependencies)接受两个参数。

@callback 为要执行的函数
@dependencies 可选依赖项，如果不指定，那么 callback 就会在每次函数组件执行完之后执行。如果指定了，则只有依赖项中的值发生变化时候 callback 才会执行。

对应到 Class 组件，useEffect 涵盖了 `ComponentDidMount` `componentDidUpdate` `componentWillUnmount`三个生命周期方法。

### useEffect 模拟生命周期

#### componentDidMount

当 useEffect 的第二个参数为空时，就是函数组件加载完成时执行。

```
useEffect(() => {
    // do something...
}, [])
```

#### componentDidUpdate

当 useEffect 的第二个参数有值时，即监听依赖项中的值发生变化时，会触发 callback.

```
useEffect(() => {
    // 当id发生变化时会重新触发该函数
    // do something...
}, [id])
```

#### componentWillUnmount

当 useEffect(callback) callback 中有返回函数时，将会用于组件销毁时做一些清理的操作。

```
const TempCom:React.FC = () => {
    useEffect(() => {
        // do anything...

        return () => {
             //  组件销毁时重新 do something...
        }
    }, [])
}
```

#### 四种不同的时机执行一个回调产生的副作用

1. useEffect(() => {}) 每次 render 后执行 callback
2. useEffect(() => {}, []) 仅第一次 render 后执行 callback
3. useEffect(() => {}, [deps]) 当 deps 发生变化后执行 callback
4. useEffect(() => {return () => {}}, []) 组件 unmount 后执行。

### useEffect 最佳实践

1. 在 useEffect 的回调函数中使用的变量，都必须在依赖项中声明；
2. 不能出现在条件语句或循环中，也不能出现在 return 后；
3. 只能在函数组件或自定义 Hooks 中使用。

## useCallback 缓存回调函数

在函数组件中，每一次 UI 的变化都需要重新执行整个函数来完成。

```
function CounterComp () {
    const [count, setCount] = useState(0);
    const countPlus = setCount(count + 1)

    return (
        <button onClick={countPlus}>Add  {count}</button>
    )
}
```

上述代码中，在 button 上添加了一个`countPlus`事件，用来让计数器加 1.但是因为`countPlus`事件是定义在函数组件内部，所以在函数组件每次渲染时无法重用`countPlus`函数，而是每次都需要重新创建一个。
而每次接收到新创建的函数，都会让接收事件处理函数的组件重新渲染。

**只有当 count 发生变化时，我们才需要重新定一个回调函数。** 而这正是**useCallback** Hook 的作用。

```
// tempComp.tsx

import React from 'react';

const TempComp: React.FC<any> = (props) => {
  console.log('temp component---->>>>', props);
  const { clickEvent } = props;

  return (
    <>
      <button onClick={clickEvent}>number</button>
    </>
  );
};

export default React.memo(TempComp);


parent.tsx

import React, {useState, useCallback} from 'react'
import TempComp from './TempComp'

const ParentComp:React.FC<any> =() => {
      const [count, setCount] = useState<number>(1);

const clickEvent = useCallback(() => {
    setCount(count + 1);
}, [count]);



return (<>
    <TempComp clickEvent={clickEvent}></TempComp>

</>)
}


```

### useCallback 最佳实践

- useCallback 需与**React.memo**配合使用。

## useMemo 缓存计算的结果

如果某个数据是通过其它数据计算得到的，那么只有当依赖的数据发生变化时，才应该重新进行计算。

useMemo(callback, deps)

- @callback 回调函数
- @deps arrary 要监听的值，当 deps 发生改变时会重新触发 callback

举个例子，对于某个用户列表，根据搜索条件展示过滤后的用户。

```
import React, { useMemo, useEffect } from 'React'

const FilterComp:React.FC<any> = () => {
    const [userList， setUserList] = useState([])
    const [filterVal, setFilterVal] = useState('')

    useEffect(() => {
        fetch('query/user/list').then(res => {
            setUserList(res.data.userList)
        })
    }, [])

    let filterList = []

    if(userList.length) {
    // 此处无论组件因为什么原因刷新重新执行，都会触发重新计算
        filterList = userList.filter(item => {
            return item.name.includes(filterVal)
        })
    }

    return (
    <>
        <input type="text" value={filterVal} />

        <div>
            filterList.length && filterList.map(item => {
                return (<div>{item.name}</div>)
            })
        </div>
    </>
    )

}

```

在上述例子中，组件如果需要重新渲染的话，只需要 filterVal 或 userList 发生变化之后再重新计算渲染。**使用 useMemo,可以避免不必要的重复计算和渲染。**

```
    filterList = useMemo(() => {
        return userList.filter(item => {
            item.name.includes(filterVal)
        })
    }, [filterVal, userList])
```

### useMemo 和 useCallback 的区别

- useMemo 缓存的结果是回调函数中 return 回来的值，主要用于缓存计算结果的值。
- useCallback 缓存的结果是函数，主要用于函数缓存。因为函数组件任何 state 的变化，都会触发整个组件的更新，而有的函数是没必要更新的，缓存起来避免资源浪费。需配合 React.memo 一起使用。

## useRef 在多次渲染之间共享数据

### 共享值

我们可以把 useRef 看做是在函数组件外创建一个容器控件，在这个容器控件上通过设置 current 属性值，从而在函数组件的多次渲染间共享这个值。

```
import React , {useState, useEffect, useRef, useCallback} from 'react'

const DelayTimer:React.FC<any> = () => {
    cont [time, setTime] = useState<number>(0);
    // 定义一个timer容器存储跨组件变量
    let timer = useRef(null)

    const startTimeHandle = useCallback(() => {
        timer.current = setInterval(() => {
            setTime((time) => time +1)
        }, 1000)
    }, [])

    const endTimeHandle = useCallback(() => {
        clearInterval(timer.current)
        timer.current = null
    }, [])

    return (
        <div>
            <button onClick={startTimeHandle}>start</button>
            <button onClick={endTimeHandle}>end</button>
        </div>
    )


}
```

**一般情况下，useRef 保存的数据是和 UI 的渲染无关的，因此在 ref 的值发生变化时是不会触发组件的重新渲染。**

### 保存 DOM 节点的引用

在 React 中，绝大多数时候我们不需要关心真实的 DOM 是如何渲染和修改的。但是在某些场景下，我们需要获得真实 DOM 的引用。所以**结果 React ref 属性和 useRef，我们就可以获得真实的 DOM 节点。**

```
fucntion InputFocusComp() {
    let inputRef = useRef(null)

    const focusInput = useCallback(() => {
        inputRef.current.focus()
    }, [])


    return (
        <input type="text" ref={inputRef}  />
        <button onClick={focusInput}>点击input 框对焦</button>
    )
}
```

## useContext 定义全局状态

React 组件之间的状态(数据)传递只有一种方式，`props`。这就意味着只能在父子组件之间进行传递。
如果要跨层次或者组件之间要进行数据的共享，就需要用到 useContext Hook.

React 提供了一个 Context 机制，能够让所有在某个组件开始的组件树上创建一个 Context。这样这个组件树上的所有组件，就都能访问和修改这个 Context 了。

`const val = useContent(MyContext)`

### createContext(initVal)

使用`React.createContext`可以从某个组件为根组件的组件树上创建一个 Context

`const MyContext = React.createContext(initVal)`

官方示例：

```

const themes = {
  light: {
    foreground: "#000000",
    background: "#eeeeee"
  },
  dark: {
    foreground: "#ffffff",
    background: "#222222"
  }
};
// 创建一个 Theme 的 Context

const ThemeContext = React.createContext(themes.light);
function App() {
  // 整个应用使用 ThemeContext.Provider 作为根组件
  return (
    // 使用 themes.dark 作为当前 Context
    <ThemeContext.Provider value={themes.dark}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

// 在 Toolbar 组件中使用一个会使用 Theme 的 Button
function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

// 在 Theme Button 中使用 useContext 来获取当前的主题
function ThemedButton() {
  const theme = useContext(ThemeContext);
  return (
    <button style={{
      background: theme.background,
      color: theme.foreground
    }}>
      I am styled by theme context!
    </button>
  );
}
```

其中，`<ThemeContext.Provider value={themes.dark}>`接收的参数可以为一个 state 值，通过修改 state 值就可以实现动态切换 Context 的值了。并且所有用到这个 Context 的地方都会自动刷新。

### useContext 实践

虽然 Context 提供了一个方便在多个组件之间共享数据的机制。但是它相当于在 React 中定义了一个全局变量，会使得调试和复用变得复杂和困难。

## Hooks 使用规则

- 只能在函数组件的顶级作用域中使用；只能在函数组件或其他 Hooks 中使用。
  顶级作用域，就是**Hooks 不能用在循环、条件判断或者嵌套函数内执行，必须是顶层。**同时 **Hooks 在组件的多次渲染之间，必须按顺序被执行。**
- 避免重复定义回调函数

---

_`20221111补充：`_

### 保持状态的一致性

在 Hooks 的使用过程中我们应当尽可能使用最优、最合理的方式去管理应用状态。

#### 保证状态最小化

不要把 State 当做变量用。**在保证 State 完整性的同时，也应保证它的最小化。**

举个栗子。我们要实现一个对订单列表进行关键字搜索的功能。我们可能需要一个搜索框和一个订单列表。

按照 React 状态驱动 UI 的思想，我们页面上可能需要三个状态：

1. 订单列表的数据：可能来自于某个接口请求；
2. 搜索关键字： 用户输入；
3. 搜索结果： 根据搜索关键字过滤原始数据。

故代码实现如下：

```
const OrderListFilter = function (data) {
  // 搜索关键字
  const [filterVal, setFilterVal] = useState('');

  // 过滤后展示的数据，默认为原始数据
  const [filterList, setFilterList] = useState(data);

  const handleFilter = useCallback(
    (evt) => {
      const temp = evt.target.value;
      setFilterVal(temp);
      setFilterList(
        filterList.filter((item) => {
          return item.title.includes(temp);
        }),
      );
    },
    [filterVal],
  );

  return (
    <div>
      <input type="text" value={filterVal} onChange={handleFilter} />
      {filterList.length
        ? filterList.map((item) => {
            return <div>item.title</div>;
          })
        : null}
    </div>
  );
};
```

在上述代码中，使用了两个 state 去保存过滤后的结果。但其实这个 state 是多余的。因为**过滤后的结果数据可以使用计算属性 useMemo 缓存起来。**

```
const OrderListFilter = function (data) {
  // 搜索关键字
  const [filterVal, setFilterVal] = useState('');

  const filterList = useMemo(() => {
    return data.filter(item => {
      return item.title.includes(filterVal)
    })
  })

  const handleFilter = useCallback(
    (evt) => {
      const temp = evt.target.value;
      setFilterVal(temp);
      setFilterList(
        filterList.filter((item) => {
          return item.title.includes(temp);
        }),
      );
    },
    [filterVal, data],
  );

  return (
    <div>
      <input type="text" value={filterVal} onChange={handleFilter} />
      {filterList.length
        ? filterList.map((item) => {
            return <div>item.title</div>;
          })
        : null}
    </div>
  );
};
```

这样就可以只使用一个 state 来使得组件最简化。
所以我们在定义一个新的状态之前，都要再三拷问自己：**这个状态是必须的吗？是否能通过计算得到？**

#### 避免中间状态，保持数据源唯一
