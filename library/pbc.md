### 1. 基本介绍

---

  `Protocol Buffers`是`Google`公司开发的一种`数据描述语言`;

  `Protocol Buffers`可以让你随意定义你的数据的结构，并生成基于各种语言的代码;

  `Protocol Buffers`不依赖于语言和平台并且可扩展性极强, 在网上可以找到大量的几乎涵盖所有语言的拓展包; 

  `Protocol Buffers`的这些数据流可以在网络中轻松地传递, 并且你也可以更新这些数据也不会受到任何的影响;

  `Protocol Buffers`必须先定义、后使用, 有完整的命名空间、RPC标准语法支持;

### 2.基本类型

---

  * `bool`: `Boolean`值

  * `double`: 浮点数

  * `float`: 单精度浮点

  * `int32`: int类型，使用可变长编码，编码负数不够高效,如果有负数那么使用`sint32`;

  * `sint32`: int类型，使用可变长编码, 有符号的整形, 比通常的`int32`高效;

  * `uint32`: 无符号整数使用可变长编码方式;

  * `int64`:  使用可变长编码方式。编码负数时不够高效——如果有负数，可以使用`sint64`;

  * `sint64`: 使用可变长编码方式。有符号的整型值。编码时比通常的`int64`高效;

  * `uint64`: 无符号整数使用可变长编码方式;

  * `fixed32`: 总是4个字节。如果数值总是比总是比`2^28`大的话，这个类型会比`uint32`高效。

  * `fixed64`: 总是8个字节。如果数值总是比总是比`2^56`大的话，这个类型会比`uint64`高效。

  * `sfixed32`: 总是4个字节。

  * `sfixed64`: 总是8个字节。

  * `string`: 一个字符串必须是`UTF-8`编码或者`7-bit ASCII`编码的文本。

  * `bytes`: 可能包含任意顺序的字节数据。类似`java`的`ByteString`以及`c++ string`;


### 3.内置方法

---

  导入`protobuf`库, `local pb = require "protobuf"`.

---

  函数原型: `pb.loadfile(pb_cp_string)`

  从一个`合法的文件`中读取`数据结构定义`, 并将其注册到`protobuf`内部;

---

  函数原型: `pb.encode (pb_struct, table)`

  第一个参数`pb_struct`表示合法的`数据结构名称`, 第二个参数`table`表示需要转换为pb格式的对象;

  此方法在成功后, 返回一个合法`pb_string`;

---

  函数原型: `pb.decode (pb_struct, pb_string)`

  第一个参数`pb_struct`表示合法的`数据结构名称`, 第二个参数`pb_string`表示需要转换为对象的合法`string`;

  此方法在成功后, 返回一个`lua table`;


### 4.使用示例

---

  本示例的所有代码, 可以从[这里](https://github.com/CandyMi/cfadmin/blob/master/script/test_protobuf.lua)找到.

```lua
local Log = require "logging"

local pb = require "protobuf"

--[[
测试与使用指南:

  1. 安装protobuf的命令行二进制生成工具protoc.

  2. 使用命令 protoc Person.proto -o Person.pb 生成协议文件.

  3. 使用loadfile读取生成的协议文件Person.pb后就完成了数据结构注册与导入.

  4. 这有时候就可以开始使用encode/decode方法进行代码测试.

  5. 需要注意的是: protobuf协议需要"先定义(注册), 后使用", 支持protobuf v2/v3版本语法.
]]

pb.loadfile("Person.pb")

local pb_string = pb.encode("Person", {
  name = "CandyMi",
  age = 2^32 - 1,
  hand = {
    left = "左手",
    right = "右手",
  },
  foot = {
    left = "左脚",
    right = "右脚",
  }
})
Log:DEBUG(pb.tohex(pb_string))

Log:DEBUG(pb.decode("Person", pb_string))

Log:DEBUG(pb.clear("Person"))
```

```protobuf
syntax = "proto3";

message Person
{
  message Hand
  {
    string left  = 1;
    string right = 2;
  }
  message Foot
  {
    string left  = 1;
    string right = 2;
  }
  string name = 1;
  uint32 age  = 2;
  Hand hand = 3;
  Foot foot = 4;
}
```

```bash
[candy@MacBookPro:~/cfadmin] $ ./cfadmin -e script/test_protobuf.lua
[2020-09-08 00:04:02,528] [@script/test_protobuf.lua:19] [DEBUG] : true, 243
[2020-09-08 00:04:02,529] [@script/test_protobuf.lua:35] [DEBUG] : {["age"]=4294967295, ["name"]="CandyMi", ["hand"]={["left"]="左手", ["right"]="右手"}, ["foot"]={["left"]="左脚", ["right"]="右脚"}}
[2020-09-08 00:04:02,529] [@script/test_protobuf.lua:37] [DEBUG] : nil
```