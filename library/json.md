### 1. 基本介绍

---
  
  `JSON(JavaScript Object Notation)` 是一种轻量级的数据交换格式。

  `JSON`是`(JavaScript Programming Language) Standard ECMA-262 3rd Edition - December 1999`实现的子集;

  `JSON` 采用完全独立于编程语言的文本格式来存储和表示数据。由于简洁和清晰的层次结构, 进而成为理想的`数据交换语言`;

  `JSON`是`Douglas Crockford`从`2001`年开始大范围推广; 同时在`2005-2006`年, 雅虎、谷歌等企业也开始使用此格式;

  `JSON`可在任何时刻使用任何类型, 不用预先定义基础结构;

### 2.基本类型

---

  `JSON`可以表示任何类型，例如: 字符串、数字、对象、数组等。但是对象和数组是比较特殊且常用的两种类型。

  * 对象 - 对象在 `JS` 中是使用花括号包裹 `{}` 起来的内容，数据结构为 `{key1：value1, key2：value2, ...}`的键值对结构。在面向对象的语言中，`key` 为对象的属性，`value` 为对应的值。键名可以使用整数和字符串来表示。值的类型可以是任意类型。

  * 数组 - 数组在 `JS` 中是方括号 `[]` 包裹起来的内容，数据结构为 `["java", "javascript", "vb", ...]` 的索引结构。在 `JS` 中，数组是一种比较特殊的数据类型，它也可以像对象那样使用键值对，但还是索引使用得多。同样，值的类型可以是任意类型。

### 3.内置方法

---

  导入`JSON`库, `local json = require "json"`

---

  函数原型: `json.encode(object)`

  将参数`object`转换合法的`json`字符串, `object`内部包含: `function`, `userdata`, `thread`等类型将会转换失败.

  返回值是`String`类型;

---

  函数原型: `json.decode(jstring)`

  将参数`jstring`转换`lua table`, 如果jstring不合法则会转换失败.

  返回值是`Table`类型;

### 4.使用示例

---

```lua
local json = require "json"
local LOG = require "logging"

local jstring = json.encode({ a = 1, b = 2 })

local tab = json.encode(jstring)

LOG:DEBUG(jstring)

LOG:DEBUG(tab)
```

```bash
[candy@MacBookPro:~/cfadmin] $ ./cfadmin
[2020-09-07 23:41:41,530] [@script/main.lua:8] [DEBUG] : "{"a":1,"b":2}"
[2020-09-07 23:41:41,531] [@script/main.lua:10] [DEBUG] : ""{\"a\":1,\"b\":2}""
[candy@MacBookPro:~/cfadmin] $
```