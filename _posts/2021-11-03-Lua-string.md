---
title:  "Lua模式匹配"
author: wangbin
categories: [ Lua ]
tags: [ Lua ]
key: k1635868800
pageview: true
---


## 模式匹配相关函数
### string.find
- `string.find` 用于在指定的目标字符串中搜索指定的模式。

```lua
s = "hello world"
i, j = string.find(s, "hello")
print(i, j)                       -- 1 5
print(string.find(s, "world"))    -- 7 11
print(string.find(s, "l"))        -- 3 3
print(string.find(s, "lll"))      -- nil
```

### string.match
- `string.match` 返回的是目标字符串中与模式相匹配的那部分子串，而非该模式所在的位置。

```lua
print(string.match("hello world", "hello"))   -- hello

date = "Today is 3/11/2021"
print(string.match(date, "%d+/%d+/%d+"))      -- 3/11/2021
```

### string.gsub
- `string.gsub` 有3个必选参数：目标字符串、模式和替换字符串，其基本用法是将目标字符串中所有出现模式的地方换成替换字符串。
- 该函数还有一个可选的第4个参数，用于限制替换的次数。

```lua
print(string.gsub("Lua is cute", "cute", "great"))  -- Lua is great
print(string.gsub"all lii", "l", "x", 1)            -- axl lii
```

### string.gmatch
- 函数string.gmatch返回一个函数，通过返回的函数可以遍历一个字符串中所有出现的指定模式。

```lua
s = "some string"
words = {}
for w in string.gmatch(s, "%a+") do
    words[#words + 1] = w
end
```

## 模式

| 模式 | 含义 |
| --- | --- |
| . | 任意字符 |
| %a | 字母 |
| %c | 控制字符 |
| %c | 数字 |
| %g | 除空格外的可打印字符 |
| %l | 小写字母 |
| %p | 标点符号 |
| %s | 空白字符 |
| %u | 大写字母 |
| %w | 字母和数字 |
| %x | 16进制数字 |
| + | 重复一次或多次 |
| * | 重复零次或多次 |
| - | 重复零次或多次（最小匹配） |
| ? | 可选（出现零次或一次） |

- 这些类的大写形式表示类的补集。例如，'%A'代表任意非字母的字符：

```lua
print(string.gsub("hello, up-down", "%A", "."))  -- hello..up.down	3
```

- 当在模式中使用时，还有一些被称为魔法字符（magic character）的字符具有特殊含义。Lua语言的模式所使用的魔法字符包括：` ( ) . % + - * ? [ ] ^ $ `。'%？'匹配一个问号，'%%'匹配一个百分号。
- 在字符集前加一个补字符^就可以得到这个字符集对应的补集：模式'[^0-7]'代表所有八进制数字以外的字符，模式'[^\n]'则代表除换行符以外的其他字符。


## 捕获
- 捕获（capture）机制允许根据一个模式从目标字符串中抽出与该模式匹配的内容来用于后续用途，可以通过把模式中需要捕获的部分放到一对圆括号内来指定捕获。

```lua
date = "Today is 3/11/2021"
d, m, y = string.match(date, "(%d+)/(%d+)/(%d+)")
print(d, m, y)   -- 3  11  2021
```

- 在模式中，形如'%n'的分类（其中n是一个数字），表示匹配第n个捕获的副本。举一个典型的例子，假设想在一个字符串中寻找一个由单引号或双引号括起来的子串。那么可能会尝试使用模式'["'].-["']'，它表示一个引号后面跟任意内容及另外一个引号；但是，这种模式在处理像"it's all right"这样的字符串时会有问题。要解决这个问题，可以捕获第一个引号然后用它来指明第二个引号：

```lua
-- 第1个捕获是引号本身，第2个捕获是引号中的内容（与'.-'匹配的子串）。
s = [[then he said: "it's all right"!]]
q, quotedPart = string.match(s, "([\"'])(.-)%1")
print(quotedPart)   -- it's all right
print(q)            -- "
```

```lua
-- 它所匹配的内容依次是：一个左方括号、零个或多个等号、另一个左方括号、任意内容（即字符串的内容）、一个右方括号、相同数量的等号及另一个右方括号：
p = "%[(=*)%[(.-)%]%1%]"  -- 匹配Lua语言中的长字符串的模式
```

```lua
-- 剔除字符串两端空格：两个定位标记（^和$）保证了我们可以获取到整个字符串。由于中间的'.-'只会匹配尽可能少的内容，所以两个'%s*'便可匹配到首尾两端的空格。
function trim(s)
    return string.gsub(s, "^%s*(.-)%s*$", "%1")
end
```

> 参考文档：《Lua程序设计（第4班）》