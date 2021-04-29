---
title: python基础语法
tags: []
originContent: "<!--more-->\n## 环境配置\n### 安装VSCode\n```\n$ bash <(curl -s https://git.forchange.cn/snippets/17/raw)\n\n$ bash <(curl -s https://git.forchange.cn/snippets/17/raw)\n\n\n```\n### 安装Python及解读器\nPython的安装环境主要涉及三个软件：Python解释器、Python编辑器（例如VScode）、pip包管理工具\n\n```\n// 安装Python解释器\n$ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py\n\n$ python --version  \n\n// 安装pip。因为我安装的是2.7的版本，所以easy_install.py已默认安装，而pip需要我们手动安装。\n$ sudo easy_install pip\n$ pip --version\n\n```\n\n##  数据类型\n\n### 字符串(str)\n只要是被【单/双/三引号】这层皮括起来的内容，不论内容是中文、英文、还是数字，都表示是字符串。\n\n```\nprint(520)\nprint('''薄梦风轻眠''')\nprint('Cinanet')\n```\n### 整数(int)\n赋值语句中的数字就是正整数、负整数和零的数，及没有小数点的数字。\n\n\n```\nprint(-20)\nprint(0)\nprint(20)\n\n```\n#### 算数运算符\n\n|元素符 | 含义 | 栗子 |\n|----  | -- |  --|\n|  +   | 加 | 1 + 1 输出结果为3 |\n| - | 减 | 3 -  1 输出结果为 2 |\n| * | 乘 | 2 * 3 输出 结果为6 |\n| / | 除 |   4 /2  输出结果为2 |\n|  % | 取模。返回除法的余数 | 5 %  2 输出结果 为1|\n| ** |幂。返回x的y次幂 | 2 ** 3 输出结果为2的三次方 |\n| // | 取整除。返回商的整除部分  | 9 / 4 结果为2|\n\n### 浮点数 (float)\n带小数点的数字，就是浮点数 。\n```\n1.0\n2.3\n5.689\n```\n\n## 数据类型转换\n\n- type() 返回数据的类型\n\n```\ntype('asd)  // <type 'str'>\ntype(1) // <type 'int'>\ntype(1.0) // <type 'float'>\n\n```\n-  str() 将其他数据类型转换为字符串\n-  int() 将其他数据类型转换为整数\n\t- 只有符合整数规范的字符串类数据，才能被int()强制转换\n\t- 文字类和小数类字符串，无法转换\n\t- 浮点数转化成整数，莫争取0 如 int(4.5) --> 4\n-  float() 将其他数据类型转换为整数\n\t- 文字类型无法转换\n\n\n\n\n\n"
categories: []
toc: false
---

<!--more-->
## 环境配置
### 安装VSCode
```
$ bash <(curl -s https://git.forchange.cn/snippets/17/raw)

$ bash <(curl -s https://git.forchange.cn/snippets/17/raw)


```
### 安装Python及解读器
Python的安装环境主要涉及三个软件：Python解释器、Python编辑器（例如VScode）、pip包管理工具

```
// 安装Python解释器
$ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py

$ python --version  

// 安装pip。因为我安装的是2.7的版本，所以easy_install.py已默认安装，而pip需要我们手动安装。
$ sudo easy_install pip
$ pip --version

```

##  数据类型

### 字符串(str)
只要是被【单/双/三引号】这层皮括起来的内容，不论内容是中文、英文、还是数字，都表示是字符串。

```
print(520)
print('''薄梦风轻眠''')
print('Cinanet')
```
### 整数(int)
赋值语句中的数字就是正整数、负整数和零的数，及没有小数点的数字。


```
print(-20)
print(0)
print(20)

```
#### 算数运算符

|元素符 | 含义 | 栗子 |
|----  | -- |  --|
|  +   | 加 | 1 + 1 输出结果为3 |
| - | 减 | 3 -  1 输出结果为 2 |
| * | 乘 | 2 * 3 输出 结果为6 |
| / | 除 |   4 /2  输出结果为2 |
|  % | 取模。返回除法的余数 | 5 %  2 输出结果 为1|
| ** |幂。返回x的y次幂 | 2 ** 3 输出结果为2的三次方 |
| // | 取整除。返回商的整除部分  | 9 / 4 结果为2|

### 浮点数 (float)
带小数点的数字，就是浮点数 。
```
1.0
2.3
5.689
```

## 数据类型转换

- type() 返回数据的类型

```
type('asd)  // <type 'str'>
type(1) // <type 'int'>
type(1.0) // <type 'float'>

```
-  str() 将其他数据类型转换为字符串
-  int() 将其他数据类型转换为整数
	- 只有符合整数规范的字符串类数据，才能被int()强制转换
	- 文字类和小数类字符串，无法转换
	- 浮点数转化成整数，莫争取0 如 int(4.5) --> 4
-  float() 将其他数据类型转换为整数
	- 文字类型无法转换

### 判断条件

###  if 单向判断
```
if xxx  :
  do something...
```

### if else 双向判断 
```
if xxx :
   do something...
else:
   do otherthing...
```

### if elif else 多项判断
```
if xxx :
  do something...
elif xxx:
  do othething...
eles:
  do anything...
```




