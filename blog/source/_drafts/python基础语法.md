---
title: python基础语法
tags:
  - 大千世界
categories:
  - python
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

## 数据类型

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

元素符含义栗子+加1 + 1 输出结果为3-减3 -  1 输出结果为 2*乘2 * 3 输出 结果为6/除4 /2  输出结果为2%取模。返回除法的余数5 %  2 输出结果 为1**幂。返回x的y次幂2 ** 3 输出结果为2的三次方//取整除。返回商的整除部分9 / 4 结果为2元素符含义栗子+加1 + 1 输出结果为3-减3 -  1 输出结果为 2*乘2 * 3 输出 结果为6/除4 /2  输出结果为2%取模。返回除法的余数5 %  2 输出结果 为1**幂。返回x的y次幂2 ** 3 输出结果为2的三次方//取整除。返回商的整除部分9 / 4 结果为2元素符含义栗子+加1 + 1 输出结果为3-减3 -  1 输出结果为 2*乘2 * 3 输出 结果为6/除4 /2  输出结果为2%取模。返回除法的余数5 %  2 输出结果 为1**幂。返回x的y次幂2 ** 3 输出结果为2的三次方//取整除。返回商的整除部分9 / 4 结果为2### 浮点数 (float)

带小数点的数字，就是浮点数 。

```
1.0
2.3
5.689
```

### 数据类型转换

- type() 返回数据的类型

```
type('asd)  // <type 'str'>
type(1) // <type 'int'>
type(1.0) // <type 'float'>
```

- str() 将其他数据类型转换为字符串
- int() 将其他数据类型转换为整数
  - 只有符合整数规范的字符串类数据，才能被int()强制转换
  - 文字类和小数类字符串，无法转换
  - 浮点数转化成整数，莫争取0 如 int(4.5) --> 4
- float() 将其他数据类型转换为整数
  - 文字类型无法转换

---

## 序列与元组

指它的成员都是有序排列，并且可以通过下标偏移量访问到它的一个或几个成员。

### 序列的基本操作

#### 根据下标取指定的元素

```
"abcdefg" // 字符串
temp = "abcdefg"
temp[0] // a
temp[0:4] // abcd 这里的4是指访问到下标为4之前的序列
temp[-1] // g

[1, 2, 3] // 列表
("abv, "sad") // 元组
```

#### 成员关系操作符 not in

判断指定的对象是否在序列中

```
temp = "abcdefg"
"c" in temp // True
"x" in temp // False
"d" not in temp // False
```

#### 连接操作符 +

```
temp = "abcdefg"
print(temp + temp) // abcdefgabcdefg
print(temp + ""1233) // abcdefg1233
```

#### 重复操作符

```
temp = "abcdefg"
print(temp * 3) // abcdefgabcdefgabcdefg
```

#### 切片操作符

```
temp = "abcdefg"
temp[0] // a
temp[-3] // 提取最后3个字符efg
temp[::-1] // 反转序列  gfedcba
```

### 元组

元组中存储的内容一般不可以变更

#### 大小比较

### 列表

列表存储的内容可以变更。操作方法均可以使用切片、重复、连接

```
tempList = ['a', 'b', 'c', 'd', 'e']
tempList.append('x') // ['a', 'b', 'c', 'd', 'e', 'x']
tempList.remove('e') // ['a', 'b', 'c', 'd', 'x']
```

```
(12) > 13 // False
(12, 13) > (13, 14) // 1213 > 1314 False
```

---

## 条件与循环

### if 单向判断

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
elif xxx and xxa :
  do todo...
eles:
  do anything...
```

### while

```
tempVal = 12
while True:
  print(tempVal)
  tempVal -= 1
  if tempVal <= 0
    break // 结束循环
    // continue // 跳过当前循环
```

### for

```
tempList = ['a', 'b', 'c', 'd', 'e']
for(item in tempList):
  print(item) // 打印出列表里的每一项 


range(13) // 循环出0~12的列表
for(let num in range(13)):
  print(num) // 循环打印 0, 1,3,..., 12
```

---

## 文件

### 内置函数和方法

#### open()

打开文件

#### read()

输入

#### readline()

输入一行

#### seek()

文件内移动

#### write()

输出

#### close()

关闭文件

#### tell()

文件指针索引

```
# file1 = open('name.txt', 'w') # w 写入； r 只读 ; a 增加
# file1.write('小乔')
# file1.close()

# file2 = open('name.txt')
# print(file2.read())
# file2.close()

# file3 = open('name.txt', 'a')
# file3.write('大乔')
# file3.close()

# file4 = open('name.txt')
# print(file4.readline())

f5 = open('name.txt')
for item in f5.readlines():
  print(item)
  print('~~~~~~')
```

## 异常处理

> try:
> (异常监控)
> except Exception [reason]
> (异常处理代码)
> finally:
> (无论异常是否发生都会执行)

### 常见的except(异常)类型

- `NameError` 变量声明异常
- `ValueError` 值类型异常
- `AttributeError`属性异常，如int中append('xxx')
- `KeyError`

## 函数

### 作用域

```
var1 = 123
def func():
  var1 = 456
  print(var1) # 456

func()
print(var1) # 123
```

若想在函数中改变全局变量的val使用`global`关键字

```
var2 = 111
def func2():
  global var2
  var2 = 222 
func2()
print(var2) # 222
```

### 可变长参数

假设定义的函数中有多个参数，调用时默认是需要顺序输入每个入参的。也可以使用命名的参数去指定要传入的参数

```
def temp_func(a, *other):
  print(len(other) + 1)
  
temp_func('123', 345, 555)
```

### 迭代器

```
list = [1, 2]
it = iter(list)
print(next(it))
print(next(it))
```

#### yield

当函数运行到yield关键字时，会暂停并且记录当前位置，当再次调用next时，返回当前运算的值并继续参与运算执行。

```
def range(start, end, step):
  x = start
  while x < end:
    yield x
    x += step
    
for item in range(10, 20, 0.5):
  print(item)
```

### lambda 表达式

当定义的函数只是用于简单的数值表达式计算时，可以考虑省略函数的名字以及函数的返回值。

```
def true():
  return true


lambda: True

def add(x, y):
  return x + y;

lambda x, y: x + y;
```

### 内置函数

#### filter

根据某种规则去过滤符合条件的值

```
arr = [1, 2, 3, 4, 5]
nextList = [9, 8, 7, 6,5]
temp1 = list(filter(lambda x: x > 3, arr))

print(temp1) # [4, 5]
```

#### map

循环遍历list，返回运算后的值

```
temp2 = list(map(lambda x: x * 2, arr))
temp3 = list(map(lambda x, y: x + y, arr, nextList))
print(temp2) # [2, 4, 6, 8, 10]
print(temp3) # [10, 10, 10, 10, 10]
```

#### reduce

定义一个初始值，按定义规则遍历之后返回一个具体的数值

```
from functools import reduce
temp4 = reduce(lambda x, y: x + y, nextList, 1)
print(temp4) # 36 累积相加
```

#### zip

可用于数据的key:val 互换,也可用于元组的矩阵转换

```
temp5 = zip((1, 2, 3), (4, 5, 6))

for i in temp5:
  print(i) # (1, 4) (2, 5) (3, 6)


dicta = {'a': 'aa', 'b':'bb'}
dictb = zip(dicta.values(), dicta.keys())
print(dict(dictb)) # {'aa': 'a', 'bb': 'b'}
```

#### range

Python3 range() 函数返回的是一个可迭代对象（类型是对象），而不是列表类型， 所以打印的时候不会打印列表。

Python3 list() 函数是对象迭代器，可以把range()返回的可迭代对象转为一个列表，返回的变量类型为列表。

Python2 [range() 函数](https://www.runoob.com/python/python-func-range.html)返回的是列表。

##### 用法

- `range(stop)`
- `range(start, stop, step)`

```

for i in range(5):
  print(i)
  
print(list(range(0, 20, 2))) # [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
```

#### 闭包

函数内部有一个函数，调用了外部函数的变量，使得该变量与内部函数同时存在，不会被销毁。

```
def closure():
  outNum = [0]
  def adder():
     outNum[0] += 1;
     return outNum[0]
  return adder


temp = closure()

print(temp()) # 1
print(temp()) # 2
print(temp()) # 3
print(temp()) # 4
```

### 装饰器

装饰器可以对函数/类进行修改、注释、或者注入方法。

```
def tips(func):
  def loger(a, b):
    print('start')
    func(a, b)
    print('end')
  return loger

@tips
def add(a, b):
  print(a + b)


print(add(4, 5))
```

## 模块

模块是在代码量变得非常大之后，为了将重复的代码放在一起，且这部分代码可以附加到程序中，附加的过程叫做`导入(import)`

### 写法

```
import 模块名称

form 模块名称 import 方法名
```

## class

对于函数/属性进行扩展继承。

```
class Player():
  def __init__(self, name, hp): #定义公共属性
      self.name = name
      self.hp = hp
  
  def print_role(self):  #定义公共方法
    print('%s %s' %(self.name, self.hp))
  
  
user1 = Player('Cinaiet', 99) #类的实例化
user2 = Player('Bill', 100)
user1.print_role()
user2.print_role()
```

### 类的封装

#### 将属性定义为私有，只有通过类方法才能做修改

```
class Player():
  def __init__(self, name, hp): #定义公共属性
      self.__name = name
      self.hp = hp
  
  def print_role(self):  #定义公共方法
    print('%s %s' %(self.__name, self.hp))
  
  
user1 = Player('Cinaiet', 99)
user2 = Player('Bill', 100)
user1.print_role()
user2.print_role()

user1.name = 'winlson'
user1.print_role() # Cinaiet 99
```

### 类的继承

```
class Monster():
  def __init__(self, hp = 100):
    self.hp = hp
  def run(self):
    print('怪物在移动')
    
class Animals(Monster):
  def __init__(self, hp = 10): # 继承父类的属性
      super().__init__(hp)
    
    
class Boss(Monster):
  def __init__(self, hp):
      super().__init__(hp=hp)
  def whoami(self):
    print('我是怪物我怕谁')


a1  = Monster(200)
print(a1.hp)
print(a1.run())

a2 = Animals(1)
print(a2.hp)
print(a2.run())

boss = Boss(1000)
print(boss.hp)
print(boss.whoami())
```

### with语句

```
class Testwith():
  def __enter__(self): # 类初始化是会被调用
    print('enter')
  def __exit__(self, exc_type, exc_val, exc_tb): #类进行结束时调用
    if exc_tb is None:
      print('正常结束')
    else: 
      print('has err %s' %(exc_tb))
      
with Testwith():
  print('Test is runing')
  raise NameError('testNameError') # 手动抛出一个异常
```

