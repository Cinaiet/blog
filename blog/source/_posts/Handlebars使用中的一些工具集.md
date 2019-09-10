---
title: Handlebars使用中的一些工具集
tags: jQuery
categories: JavaScript
date: 2017-10-19 10:51:07
---

- 分离以逗号隔开的数据
```
Handlebars.registerHelper('splitDetailImg',function (value) {
        var splitvalue=value.split(',');
        var reValues=[];
        for ( var i=0; i<splitvalue.length; i++ ){
            var reValue={};
            reValue.name=splitvalue[i];
            reValues[i]=reValue;
        }
        return reValues;
    });
```
<!-- more -->

- 比较两个值
```
    //sym可以是<,>,=
    Handlebars.registerHelper('compare',function (first, sym, sec) {
        var r=first+sym+sec;
        return eval(r);
    });
```
- 根据value返回不同的值
```
  //判断value的状态，渲染不同的数据
    Handlebars.registerHelper('valueCompare', function(left, operator, right, options) {
        if (arguments.length < 3) {
            throw new Error('Handlerbars Helper "compare" needs 2 parameters');
        }
        var operators = {
            '==':     function(l, r) {return l == r; },
            '===':    function(l, r) {return l === r; },
            '!=':     function(l, r) {return l != r; },
            '!==':    function(l, r) {return l !== r; },
            '<':      function(l, r) {return l < r; },
            '>':      function(l, r) {return l > r; },
            '<=':     function(l, r) {return l <= r; },
            '>=':     function(l, r) {return l >= r; },
            'typeof': function(l, r) {return typeof l == r;
            }
        };

        if (!operators[operator]) {
            throw new Error('Handlerbars Helper "compare" doesn\'t know the operator ' + operator);
        }

        var result = operators[operator](left, right);

        if (result) {
            return options.fn(this);
        } else {
            return options.inverse(this);
        }
    });
```
- 根据条件将数组分割成多个数组，用于分页等
```
Handlebars.registerHelper('cateList',function (value) {
    var valLen = value.length;
    var pageLen = 8;
    var result = [];
    for (var i = 0; i < valLen; i += pageLen){
        result.push(value.slice(i,i+pageLen));
    }
    return result;

}); 
```

