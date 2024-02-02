## 介绍

---


  `PEG`的原意: 解析表达文法（英语:`Parsing Expression Grammar`）是一种分析型形式文法。它在`2004`年由布莱恩·福特（`Bryan Ford`）推出，可以方便地、无歧义地表达一个语法。

  `LPEG`是`PEG`算法的`Lua`语言实现, 它把`PEG`的每个解析单元(`Parsing Unit`)或模式都抽象位语言中的一类对象(`first class`). 这样可以让它可以被`Lua` 的内置操作符、控制语句将其组合起来. 使得它可以拥有非常强大的语法表达式.

## 更多

---

  在[这里](https://github.com/leafo/moonscript/blob/master/moonscript/parse.moon)可以查看到使用`LPEG`来解析语法的案例, [这里](http://www.inf.puc-rio.br/~roberto/lpeg/)则可以查看`LPEG`作者对其做的详细使用介绍.