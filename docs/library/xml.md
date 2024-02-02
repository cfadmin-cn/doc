
### 1. 基本介绍

---

  可扩展标记语言、标准通用标记语言的子集简称`XML`。它非常适合万维网传输，提供统一的方法来描述和交换独立于应用程序或供应商的结构化数据。

  早在`1998`年，`W3C`就发布了`XML 1.0`规范。 是`Internet`环境中跨平台的、依赖于内容、处理分布式结构信息的有效工具。

  `XML`有两个先驱：`SGML`和`HTML`，这两个都是非常成功的标记语言，但是都俱有缺陷。`XML`正是为了解决它们的不足而诞生的。

### 2.基本类型

---

  * `byte`  有正负的 8 位整数

  * `decimal` 十进制数

  * `int` 有正负的 32 位整数

  * `integer` 整数值

  * `long`  有正负的 64 位整数

  * `negativeInteger` 仅包含负值的整数 ( .., -2, -1.)

  * `nonNegativeInteger`  仅包含非负值的整数 (0, 1, 2, ..)

  * `nonPositiveInteger`  仅包含非正值的整数 (.., -2, -1, 0)

  * `positiveInteger` 仅包含正值的整数 (1, 2, ..)

  * `short` 有正负的 16 位整数

  * `unsignedLong`  无正负的 64 位整数

  * `unsignedInt` 无正负的 32 位整数

  * `unsignedShort` 无正负的 16 位整数

  * `unsignedByte`  无正负的 8 位整数
  
### 3.内置方法

---

  导入`xml`库, `local xml2lua = require "xml2lua"`.

---

  函数原型: `xml2lua.load(filename)`

  将参数`filename`的文件内容当做xml格式进行解析, 解析成功后将返回`Lua Table`.

---

  函数原型: `xml2lua.parser(xmlstring)`

  将参数`xmlstring`字符串当做xml格式进行解析, 解析成功后将返回`Lua Table`.  

---

  函数原型: `xml2lua.toxml(lua_table, scheme_name)`

  将参数`lua_table`解析成为合法的`XML`字符串. `scheme_name`为`root`域.


### 4. 使用示例

```lua
local xml2lua = require "xml2lua"
local LOG = require "logging"

local xml = [[
<root>
  <xml>
    <Tiger>
      <food><![CDATA[meta]]></food>
      <name><![CDATA[老虎]]></name>
    </Tiger>
    <Et/>
    <People>
      <man>
        <item><![CDATA[水果糖]]></item>
        <item><![CDATA[車先生]]></item>
      </man>
      <woman>
        <dream>
          <item><![CDATA[买买买]]></item>
          <item><![CDATA[玩玩玩]]></item>
          <item><![CDATA[逛逛逛]]></item>
        </dream>
        <item><![CDATA[肉肉]]></item>
        <item><![CDATA[小宝贝]]></item>
        <item><![CDATA[小QQ]]></item>
        <item><![CDATA[車爪鱼]]></item>
      </woman>
    </People>
    <Lion>
      <food><![CDATA[meta]]></food>
      <name><![CDATA[狮子]]></name>
    </Lion>
  </xml>
</root>
]]

local xtable = xml2lua.parser(xml)
LOG:DEBUG(xtable)

LOG:DEBUG(xml2lua.toxml(xtable, "root"))
```


```bash
[candy@MacBookPro:~/cfadmin] $ ./cfadmin
[2020-09-06 22:14:58,758] [@script/main.lua:36] [DEBUG] : {["xml"]={["Tiger"]={["name"]="老虎", ["food"]="meta"}, ["People"]={["man"]={["item"]={[1]="水果糖", [2]="車先生"}}, ["woman"]={["item"]={[1]="肉肉", [2]="小宝贝", [3]="小QQ", [4]="車爪鱼"}, ["dream"]={["item"]={[1]="买买买", [2]="玩玩玩", [3]="逛逛逛"}}}}, ["Et"]={}, ["Lion"]={["name"]="狮子", ["food"]="meta"}}}
[2020-09-06 22:14:58,758] [@script/main.lua:38] [DEBUG] : "<root>
  <xml>
    <Tiger>
      <food><![CDATA[meta]]></food>
      <name><![CDATA[老虎]]></name>
    </Tiger>
    <Et/>
    <People>
      <man>
        <item><![CDATA[水果糖]]></item>
        <item><![CDATA[車先生]]></item>
      </man>
      <woman>
        <dream>
          <item><![CDATA[买买买]]></item>
          <item><![CDATA[玩玩玩]]></item>
          <item><![CDATA[逛逛逛]]></item>
        </dream>
        <item><![CDATA[肉肉]]></item>
        <item><![CDATA[小宝贝]]></item>
        <item><![CDATA[小QQ]]></item>
        <item><![CDATA[車爪鱼]]></item>
      </woman>
    </People>
    <Lion>
      <food><![CDATA[meta]]></food>
      <name><![CDATA[狮子]]></name>
    </Lion>
  </xml>
</root>
"
```