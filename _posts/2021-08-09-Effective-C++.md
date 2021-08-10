---
layout: post
title:  "Effective C++"
author: wangbin
categories: [ 编程语言 ]
tags: [ C++ ]
---

## 1、让自己习惯C++

### 条款01：视C++为一个语言联邦
- 将 `C++` 视为一个由相关语言组成的联邦而非单一语言：`C`、`Object-Oriented C++`、`Template C++`、`STL`。
- `C++` 高效编程守则视状况而变化，取决于你使用 `C++` 的哪一部分。

### 条款02：尽量以 `const，enum，inline` 替换 `#define`
- 例如将 `#define ASPECT_RATIO 1.653` 替换为 `const double AspectRatio = 1.653;`
- 对于单纯常量，最好以 `const` 对象或 `enums` 替换 `#defines`。
- 对于形似函数的宏（macros），最好改用 `inline` 函数替换 `#defines`。

### 条款03：尽可能使用const
- 将某些东西声明为 const可帮助编译器侦测出错误用法。const可被施加于任何作用域内的对象、函数参数、函数返回类型、成员函数本体。
- 当 const和non-const成员函数有着实质等价的实现时，令non-const版本调用const版本可避免代码重复。

### 条款04：确定对象被使用前已先被初始化
- 为内置型对象进行手工初始化，因为C++不保证初始化它们。
- 构造函数最好使用成员初值列（member initialization list），而不要在构造函数本体内使用赋值操作（assignment）（这种方法会首先调用默认构造函数为成员变量设初值，然后又立即赋新值）。初值列列出的成员变量，其排列次序应该和它们在class中的声明次序相同。

## 2、构造、析构、赋值运算

### 条款05：了解C++默默编写并调用哪些函数
- 编译器可以暗自为class创建default构造函数、copy构造函数、copy assignment 操作符，以及析构函数。
```cpp
class Empty { };
class Empty {
public:
    Empty() { ... }                 // 默认构造函数
    Empty(const Empty &rhs) { ... } // 拷贝构造函数
    ~Empty() { ... }                // 析构函数
    Empty& operator=(const Empty &rhs) { ... }  // 操作符重载
}
```

### 条款06：若不想使用编译器自动生成的函数，就该明确拒绝
- 为驳回编译器自动（暗自）提供的机能，可将相应的成员函数声明为private并且不予实现。

### 条款07：为多态基类声明virtual析构函数

