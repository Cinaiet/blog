---
title: 自定义Hooks
date: 2022-11-04 09:34:08
categories: JavaScript
tags:
  - 花满楼
  - 重学React
---

要用好 React Hooks 有一点很重要，就是要能够从 Hooks 的角度去思考问题。在做功能开发时，首先问自己一个问题：**这个功能中的哪些逻辑可以抽成独立的 Hooks**。

<!--more-->

之所以要抽离成独立的 hooks,会有助于代码模块化和解耦，便于后期维护。

### 如何创建自定义 Hooks

自定义 Hooks 的创建其实很简单，就是**声明一个以 use 开头的函数**，如 useCounter。
这个函数在形式上和普通的函数没有任何区别，可以接收入参，也可以有返回值。

```JavaScript
import React, { useState, useCallback } from 'react';

const useCounter = () => {
  const [count, setCount] = useState(0);

  // 加
  const plus = useCallback(() => {
    setCount(count + 1);
  }, [count]);

  // 减
  const minus = useCallback(() => {
    setCount(count - 1);
  }, [count]);

  // 重置

  const reset = useCallback(() => {
    setCount(0);
  }, []);

  return {
    count,
    plus,
    minus,
    reset,
  };
};

export default React.memo(useCounter);

// 有了这个hook 就可以在业务组件中使用。

// temp.tsx

import React from 'react';
import { Button } from 'antd';
import { useCounter } from '@/components/useCustom';

type TempCompType = {};
const TempComp: React.FC<TempCompType> = () => {
  const { count, plus, minus, reset } = useCounter();
  console.log('ggg');

  return (
    <>
      <div>
        <div>{count}</div>
        <Button onClick={plus}>plus</Button>
        <Button onClick={minus}>minus</Button>
        <Button onClick={reset}>reset</Button>
      </div>
    </>
  );
};

export default TempComp;


```

#### 特点

- **单独抽离为 Hooks，可以更方便使得逻辑复用，也能让代码更加语义化，便于理解和维护。**
- 名字以 use 开头，这样可以使 React 知道这是一个 Hook
- 函数内部一定调用了其它的 Hooks,可以使内置 hooks，也可以是自定义 hooks。只有这样才能使组件刷新。

### 封装通用逻辑

比如在日常开发中，**发起异步请求并在页面上显示**，是非常常见的需求，在这个过程中不仅要关心请求是否正确返回，也要兼顾 pendding 状态下页面上的 loading 呈现。

```JavaScript
// useAsync.ts

import React, { useState, useCallback } from 'react';

/**
* @params asyncFn 接收一个发出接口请求的promise函数。
*/

export const useAsync = (asyncFn) => {
  const [loading, setLoading] = useState(true); // 页面loading状态
  const [data, setData] = useState(null); // 请求成功拿到的res
  const [err, setErr] = useState(null);  // 请求失败拿到的error

  const execute = useCallback(() => {
    setLoading(true);
    setData(null);
    setErr(null);
    return asyncFn()
      .then((res) => {
        setData(res.data);
        setLoading(false);
      })
      .catch((error) => {
        setLoading(false);
        setErr(error);
      });
  }, [asyncFn]);

  return { execute, loading, data, err };
};

// 使用
// xxx.tsx

import React, {useState} from 'react'
import { useAsync } from '@/components/useAsync';
import { querySysAiListService } from './../../services/query';

type TempCompType = {}
const TempComp:React.FC<TempCompType> = () => {
  const {data, loading} = useAsync()

  return (
    <>
      {
        loading ? <div>loading...</div> : <div>
          {data.map(item => {
            return (<div>item.name</div>)
          })}
        </div>
      }
    </>
  )
}

```

上述例子可以看到，使用 useAsync **可以很好的将组件中的某一部分分离出去，从而实现通用逻辑的重用。**

### 拆分复杂组件

当某个组件功能越来越复杂的时候，我们会发现组件代码会变的非常长，超过 500-1000 行，甚至更多，这个时候就会变的非常难维护。

所以**保持每个函数的短小**就成了函数组件的最佳实践。尽量将相关的逻辑做成独立的 Hooks,然后在函数组件中使用这些 Hooks,通过参数传递和返回值让 Hooks 之间完成交互。

注意：**拆分 Hooks 的目的并一定是逻辑复用，也可以仅仅是业务逻辑的隔离**。

举个例子，我们需要展示一个文章列表并且支持筛选。后端提供两个接口，分别获取文章列表和文章分类，根据文章类别展示文章列表。

```JavaScript
  // 获取文章分类...
  // 获取文章列表...
  // 组合数据...
  // 根据条件筛选文章...


import React, { useCallback, useEffect, useMemo, useState } from 'react';
import { Select } from 'antd';
import { useAsync } from '@/components/useAsync';
import { queryArticleSercice, queryCategriesService } from './services/query';

const useQueryArticle = () => {
  const {
    data: articleList,
    execute,
    err: articleErr,
  } = useAsync(
    useCallback(async () => {
      const res = await queryArticleSercice();
      return res;
    }, []),
  );

  useEffect(() => execute(), [execute]);

  return {
    articleList,
    articleErr,
  };
};

const useQueryCategries = () => {
  const {
    data: categriesList,
    execute,
    err: categriesErr,
  } = useAsync(
    useCallback(async () => {
      const res = await queryCategriesService();
      return res;
    }, []),
  );

  useEffect(() => execute(), [execute]);

  return {
    categriesList,
    categriesErr,
  };
};

const useCombineList = (articleList, categriesList) => {
  return useMemo(() => {
    if (!articleList || !categriesList) return [];
    return articleList.map((item) => {
      return {
        ...item,
        category: categriesList.find((sub) => sub.id === item.category),
      };
    });
  }, [articleList, categriesList]);
};

const useFilterArticleList = (articleList, filterVal) => {
  return useMemo(() => {
    return articleList.filter((item) => {
      item.category.name === filterVal;
    });
  }, [articleList, filterVal]);
};

type TempCompType = {};
const TempComp: React.FC<TempCompType> = () => {
  // 获取文章分类...
  // 获取文章列表...
  // 组合数据...
  // 根据条件筛选文章...

  const [filterVal, setFilterVal] = useState('');

  const { articleList } = useQueryArticle(); // 获取文章列表
  const { categriesList } = useQueryCategries(); // 获取文章分类

  const combineList = useCombineList(articleList, categriesList); // 组合数据

  const ret = useFilterArticleList(combineList, filterVal);

  const options = useMemo(() => {
    return categriesList.map((item) => {
      return {
        label: item.name,
        value: item.value,
      };
    });
  }, [categriesList]);


  if(!ret) return "loading dom..."

  return (
    <>
      <Select
        value={filterVal}
        onChange={(value) => setFilterVal(value)}
        options={options}
        placeholder="选择分类"
      ></Select>
      <div>
        {ret.length &&
          ret.map((item) => {
            return (
              <div>
                <span>文章名称:{item.name}</span>
                <span>文章分类:{item.category.name}</span>
              </div>
            );
          })}
      </div>
    </>
  );
};

export default TempComp;


```

这样我们就把一个较为复杂的 hooks 拆分为了一个个独立的 hooks。隔离了业务，语义也更明确。
