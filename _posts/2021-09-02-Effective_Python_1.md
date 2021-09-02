---
title:  "Effective Python 读书笔记 - 第1章 培养Pythonic思维"
author: wangbin
categories: [ 编程语言 ]
tags: [ Python ]
key: k1630512000
pageview: true
---

## 第1条 查询自己使用的Python版本

```bash
root@ubuntu:~# python --version
Python 2.7.17
root@ubuntu:~# python3 --version
Python 3.6.9
```

## 第2条 遵循PEP 8风格指南
https://www.python.org/dev/peps/pep-0008
- 编写Python代码时，总是应该遵循PEP 8风格指南。
- 与广大Python开发者采用同一套代码风格，可以使项目更利于多人协作。
- 采用一致的风格编写代码，代码的后续修改更容易。

## 第3条 了解bytes与str的区别
- bytes包含的是由8位值所组成的序列，str包含的是由Unicode码点所组成的序列。
- 我们可以编写辅助函数来确保程序收到的字符序列确实是期望要操作的类型（要知道自己想操作的到底是Unicode码点，还是原始的8位值。用UTF-8标准给字符串编码，得到的就是这样的一系列8位值）。
- bytes与str这两种实例不能在某些操作符（例如>、==、+、%操作符）上面混用。
- 从文件中读取二进制数据（或者把二进制数据写入文件）时，应该用'rb'（'wb'）这样的二进制模式打开文件。
- 如果要从文件中读取（或者要写入文件之中）的是Unicode数据，那么必须注意系统默认的文本编码方案。若无法肯定，可通过encoding参数明确指定。

## 第4条 用支持插值的f-string取代C风格的格式字符串与str.format方法

```python
a = 1
b = 2
# C风格
print('a=%d, b=%d', a, b)

# str.format
print('a={}, b={}'.format(a, b))

# f-string，Python 3.6 新增的特性
print(f'a={a}, b={b}')
```

## 第5条 用辅助函数取代复杂的表达式
- Python的语法很容易把复杂的意思挤到同一行表达式里，这样写很难懂。复杂的表达式，尤其是那种需要重复使用的复杂表达式，应该写到辅助函数里面。
- 用if/else结构写成的条件表达式，要比用or与and写成的Boolean表达式更好懂

## 第6条 把数据结构直接拆分到多个变量里，不要专门通过下标访问

```python
item = ('a', 'b', 'c')
a, b, c = item

snacks = [('bacon', 350), ('donut', 240), ('muffin', 190)]
for rank, (name, calories) in enumerate(snacks, 1):
    print(f'#{rank}: {name} has {calories} calories')
"""输出
#1: bacon has 350 calories
#2: donut has 240 calories
#3: muffin has 190 calories
"""
```

- unpacking是一种特殊的Python语法，只需要一行代码，就能把数据结构里面的多个值分别赋给相应的变量。
- unpacking在Python中应用广泛，凡是可迭代的对象都能拆分，无论它里面还有多少层迭代结构。
- 尽量通过unpacking来拆解序列之中的数据，而不要通过下标访问，这样可以让代码更简洁、更清晰。

## 第7条 尽量用enumerate取代range
- Python内置的range函数适合用来迭代一系列整数。

```python
flavor_list = ['vanilla', 'chocolate', 'pecan', 'strawberry']
for flavor in flavor_list:
    print(f'{flavor} is delicious')

for i, flavor in enumerate(flavor_list):
    print(f'{i + 1}: {flavor}')

for i, flavor in enumerate(flavor_list, 1):
    print(f'{i}: {flavor}')
"""输出
1: vanilla
2: chocolate
3: pecan
4: strawberry
"""
```

- enumerate函数可以用简洁的代码迭代iterator，而且可以指出当前这轮循环的序号。
- 不要先通过range指定下标的取值范围，然后用下标去访问序列，而是应该直接用enumerate函数迭代。
- 可以通过enumerate的第二个参数指定起始序号（默认为0）。

## 第8条 用zip函数同时遍历两个迭代器

```python
names = ['Cecilia', 'Lise', 'Marie']
counts = [len(n) for n in names]
for name, count in zip(names, counts):
    print(f'{name}: {count}')
"""输出
Cecilia: 7
Lise: 4
Marie: 5
"""

names.append('Rosalind')
import itertools
for name, count in itertools.zip_longest(names, counts):
    print(f'{name}: {count}')
"""输出
Cecilia: 7
Lise: 4
Marie: 5
Rosalind: None
"""
```

- 内置的zip函数可以同时遍历多个迭代器。
- zip会创建惰性生成器，让它每次只生成一个元组，所以无论输入的数据有多长，它都是一个一个处理的。
- 如果提供的迭代器的长度不一致，那么只要其中任何一个迭代完毕，zip就会停止。
- 如果想按最长的那个迭代器来遍历，那就改用内置的itertools模块中的zip_longest函数。

## 第9条 不要在for与while循环后面写else块
- Python有种特殊的语法，可以把else块紧跟在整个for循环或while循环的后面。
- 只有在整个循环没有因为break提前跳出的情况下，else块才会执行。
- 把else块紧跟在整个循环后面，会让人不太容易看出这段代码的意思，所以要避免这样写。

## 第10条 用赋值表达式减少重复代码
- 赋值表达式（assignment expression）是Python 3.8新引入的语法

```python
a = b   # 普通的赋值语句
a := b  # 赋值表达式

fresh_fruit = {
    'apple': 10,
    'banana': 8,
    'lemon': 5,
}
if (count := fresh_fruit.get('apple', 0)) >= 10:
    print('apple')

def pick_fruit():
    pass
while fresh_fruit := pick_fruit():
    print(fresh_fruit)
```

- 赋值表达式通过海象操作符（:=）给变量赋值，并且让这个值成为这条表达式的结果，于是，我们可以利用这项特性来缩减代码。
- 如果赋值表达式是大表达式里的一部分，就得用一对括号把它括起来。虽说Python不支持switch/case与do/while结构，但可以利用赋值表达式清晰地模拟出这种逻辑。
