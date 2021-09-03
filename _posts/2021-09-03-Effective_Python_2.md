---
title:  "Effective Python 读书笔记 - 第2章 列表与字典"
author: wangbin
categories: [ 编程语言 ]
tags: [ Python ]
key: k1630598400
pageview: true
---

## 第11条 学会对序列做切片
- 最基本的写法是用somelist[start:end]这一形式来切割，也就是从start开始一直取到end这个位置，但不包含end本身的元素。切割出来的列表是一份全新的列表

```python
a       = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']
a[:]    # ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']
a[:5]   # ['a', 'b', 'c', 'd', 'e']
a[:-1]  # ['a', 'b', 'c', 'd', 'e', 'f', 'g']
a[4:]   #                     ['e', 'f', 'g', 'h']
a[-3:]  #                          ['f', 'g', 'h']
a[2:5]  #           ['c', 'd', 'e']
a[2:-1] #           ['c', 'd', 'e', 'f', 'g']
a[-3:-1]#                          ['f', 'g']
```

- 切片可以出现在赋值符号的左侧，表示用右侧那些元素把原列表中位于这个范围之内的元素换掉，不要求两边元素个数相同

```python
a = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']
a[2:7] = [99, 22, 14]
# a 变成：['a', 'b', 99, 22, 14, 'h']
a[2:3] = [47, 11]
# a 变成：['a', 'b', 47, 11, 22, 14, 'h']
```

```python
a = [1, 2, 3]
b = a
print(a, b)
# ([1, 2, 3], [1, 2, 3])

a[:] = [101, 102, 103]
print(a, b)
# ([101, 102, 103], [101, 102, 103])
```

## 第12条 不要在切片里同时指定起止下标与步进
- Python还有一种特殊的步进切片形式，也就是 `somelist[start:end:stride]`。这种形式会在每n个元素里面选取一个，这样很容易就能把奇数位置上的元素与偶数位置上的元素分别通过 `x[::2]` 与 `x[1::2]` 选取出来
- 同时指定切片的起止下标与步进值理解起来会很困难。
- 如果要指定步进值，那就省略起止下标，而且最好采用正数作为步进值，尽量别用负数。
- 不要把起始位置、终止位置与步进值全都写在同一个切片操作里。如果必须同时使用这三项指标，那就分两次来做（其中一次隔位选取，另一次做切割），也可以改用itertools内置模块里的islice方法。

## 第13条 通过带星号的unpacking操作来捕获多个元素，不要用切片

```python
car_ages = [0, 9, 4, 8, 7, 20, 19, 1, 6, 15]
car_ages_desceding = sorted(car_ages, reverse=True)
oldest, second_oldest, *others = car_ages_descending
oldest, *others, yongest = car_ages_descending
```
- 使用这种写法时，至少要有一个普通的接收变量与它搭配，否则就会出现SyntaxError。

## 第14条 用sort方法的key参数来表示复杂的排序逻辑

```python
class Tool:
    def __init__(self, name, weight):
        self.name = name
        self.weight = weight

    def __repr__(self):
        return f'Tool({self.name!r}, self.weight)'

tools = [
    Tool('level', 3.5),
    Tool('hammer', 0.25),
    Tool('screwdriver', 0.5),
    Tool('chisel', 0.25),
]

tools.sort(key=lambda x: x.name)
# 先按weight排序，再按name排序。reverse=True表示逆序
tools.sort(key=lambda x: (x.weight, x.name), reverse=True)
# weight逆序，name正序
tools.sort(key=lambda x: (-x.weight, x.name))
```

## 第15条 不要过分依赖给字典添加条目时所用的顺序
- 在Python 3.5与之前的版本中，dict所提供的许多方法（包括keys、values、items与popitem等）都不保证固定的顺序。
- 从Python 3.6开始，字典会保留这些键值对在添加时所用的顺序，而且Python3.7版的语言规范正式确立了这条规则。

## 第16条 用get处理键不在字典中的情况，不要使用in与KeyError
- 有四种办法可以处理键不在字典中的情况：in表达式、KeyError异常、get方法与setdefault方法。
- 如果跟键相关联的值是像计数器这样的基本类型，那么get方法就是最好的方案；如果是那种构造起来开销比较大，或是容易出异常的类型，那么可以把这个方法与赋值表达式结合起来使用。
- 即使看上去最应该使用setdefault方案，也不一定要真的使用setdefault方案，而是可以考虑用defaultdict取代普通的dict。

## 第17条 用defaultdict处理内部状态中缺失的元素，而不要用setdefault
- 如果你管理的字典可能需要添加任意的键，那么应该考虑能否用内置的collections模块中的defaultdict实例来解决问题。
- 如果这种键名比较随意的字典是别人传给你的，你无法把它创建成defaultdict，那么应该考虑通过get方法访问其中的键值。然而，在个别情况下，也可以考虑改用setdefault方法，因为那样写更短。

```python
from collections import defaultdict

class Visits:
    def __init__(self):
        self.data = defaultdict(set)

    def add(self, country, city):
        self.data[country].add(city)
```

## 第18条 学会利用__missing__构造依赖键的默认值

```python
class Pictures(dict):
    def __missing__(self, key):
        value = open_picture(key)
        self[key] = value
        return value

pictures = Pictures()
handle = pictures[path]
handle.seek(0)
image_data = handle.read()
```

- 如果创建默认值需要较大的开销，或者可能抛出异常，那就不适合用dict类型的setdefault方法实现。
- 传给defaultdict的函数必须是不需要参数的函数，所以无法创建出需要依赖键名的默认值。
- 如果要构造的默认值必须根据键名来确定，那么可以定义自己的dict子类并实现__missing__方法。