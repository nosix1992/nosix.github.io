---
title: Lua特别之处笔记
date: 2015-02-03 18:12:15
tags:
- Lua
categories:
-  Script
---


# 2.2 Booleans

两个取值 false 和 true。但要注意 Lua 中所有的值都可以作为条件。在控制结构的条
件中除了 false 和 nil 为假，其他值都为真。所以 Lua 认为 0 和空串都是真。



# 2.5 table

我们用一个疑问来引入table的介绍

## 传的是值还是引用?

lua的函数调用传的是值还是引用?

**. . .**<!-- more -->

### 测试代码

``` lua
tTableForTest = {}

tTableForTest[1] = 9

function testTable(tTable)
    tTable[1] = 11
end

print("tTableForTest[1]".." : "..tTableForTest[1])
testTable(tTableForTest)
print("tTableForTest[1]".." : "..tTableForTest[1])

print("\n==================\n")

nNumberForTest = 1
function TestNumber( nNumber )
    nNumber = 99
end

print("nNumberForTest".." : "..nNumberForTest)
TestNumber(nNumberForTest)
print("nNumberForTest".." : "..nNumberForTest)

print("\n==================\n")

nStringForTest = "hi"
function TestNumber( nString )
    nString = "hello"
end

print("nStringForTest".." : "..nStringForTest)
TestNumber(nStringForTest)
print("nStringForTest".." : "..nStringForTest)
```

### 打印结果

    tTableForTest[1] : 9
    tTableForTest[1] : 11

    ==================

    nNumberForTest : 1
    nNumberForTest : 1

    ==================

    nStringForTest : hi
    nStringForTest : hi


### 结论

- table传引用
- number传值
- string传值



## 为什么lua中table会不一样

在Lua中, table 既不是值也不是变量而是对象.

程序仅持有一个对他们的引用, Lua不会暗中产生table的副本或创建新的table.

事实上, table的创建是通过构造表达式完成的, 最简单的构造表达式就是{}

``` lua
a = {}
k = "x"
a[k] = 10
a[20] = "great"
print(a["x"])   --> 10

k = 20
print(a[k])     --> "great"
a["x"] = a["x"] + 1
print(a["x"])   --> 11

b = a           -- b与a引用了同一个table
print(b["x"])   --> 10
b["x"] = 20
print(a["x"])   --> 20

a = nil         -- 现在只有b还在引用table
b = nil         -- 再也没有对table的引用了
```

当一个程序再也没有对一个table的引用时, Lua的垃圾收集器最终会删除该table, 并
复用它的内存.


## a[x]和a.x是不同的

`a.x`表示` a["x"] ` , 表示以字符串`"x"`来索引table

`a[x] ` 是以变量x的值来索引table

``` lua
a = {}
x = "y"
a[x] = 10
print(a[x])     --> 表示a["y"], 即 10
print(a.x)      --> 表示a["x"], 没有定义这个, 所以是 nil
print(a.y)      --> 表示a["y"], 即 10
```

## 用table作为数组时

就Lua的习惯而言, 数组通常以1作为索引的起始值, 并且还有不少lua机制依赖于这个惯例, 大多数lua内置的函数都假设数组起始于索引1, 这跟c语言以0为起始是不同的.

Lua5.1以上, 可以使用 ` # ` 来返回一个数组或者线性表的最后一个索引值或者其大小.

``` lua
-- 打印所有的行
for i=1, #a do
    print(a[i])
end
```