## 一、基本介绍

  通过合理的打印适当的信息, 可以有效的帮助开发者`快速定位`与`问题排错`;

  对应用程序的`静态调试`会让人产生依赖性, 且`静态调试`也不是万能灵药;


## 二、API介绍

---

  `class`库可以在任意地方直接导入.

```lua
local LOG = require "logging"
```

### 1. LOG:DEBUG

---

  函数原型: `LOG:DEBUG(...)`

### 2. LOG:INFO

---

  函数原型: `LOG:INFO(...)`

### 3. LOG:WARN

---

  函数原型: `LOG:WARN(...)`

### 4. LOG:ERROR

---

  函数原型: `LOG:ERROR(...)`


## 三、使用示例

### 1. 代码

---

```lua
-- script/main.lua
--[[
  日志库分为2种使用方式: 1. 初始化后使用, 2. 直接导入使用;  两者之间的差异根据不同需求不同使用.

  初始化使用时为了将不同的日志打印并dump到不同的日志文件中, 这中方式在调试开发的时候非常有用.

  直接导入使用不会将日志持久化到磁盘(输出到stdout), 相对于dump到本地磁盘. 它更是适用于docker这种做为集中式日志收集.

  `path`参数通常是一个日志文件名, 它的前缀为`logs/{your_path}`, 但是你可以根据实际情况加上路径对文件进行分割.

  `dump`参数决定是否将打印内容序列化到磁盘.

  注意: 如果您的path中包含了目录, 需要先自行创建目录. 否则将会打印错误.
]]

local LOG = require "logging"
-- 仅输出到stdout
LOG:INFO('this is INFO LOG', nil, 1, nil)
LOG:DEBUG('this is DEBUG LOG', nil, nil, 1)
LOG:WARN('this is WARN LOG', 1, nil, nil)
LOG:ERROR('this is ERROR LOG', nil, nil, nil)

local log = LOG:new { path = 'main' , dump = true }
-- dump到磁盘
log:INFO('this is INFO LOG', nil, 1, nil)
log:DEBUG('this is DEBUG LOG', nil, nil, 1)
log:WARN('this is WARN LOG', 1, nil, nil)
log:ERROR('this is ERROR LOG', nil, nil, nil)
```

### 2. 输出

---

```bash
[candy@MacBookPro:~/cfadmin] $ ./cfadmin -e script/test_logging.lua
[2020-09-07 22:02:20,489] [@script/test_logging.lua:18] [INFO] : "this is INFO LOG", nil, 1, nil
[2020-09-07 22:02:20,489] [@script/test_logging.lua:19] [DEBUG] : "this is DEBUG LOG", nil, nil, 1
[2020-09-07 22:02:20,489] [@script/test_logging.lua:20] [WARN] : "this is WARN LOG", 1, nil, nil
[2020-09-07 22:02:20,489] [@script/test_logging.lua:21] [ERROR] : "this is ERROR LOG", nil, nil, nil
[2020-09-07 22:02:20,489] [@script/test_logging.lua:25] [INFO] : "this is INFO LOG", nil, 1, nil
[2020-09-07 22:02:20,489] [@script/test_logging.lua:26] [DEBUG] : "this is DEBUG LOG", nil, nil, 1
[2020-09-07 22:02:20,490] [@script/test_logging.lua:27] [WARN] : "this is WARN LOG", 1, nil, nil
[2020-09-07 22:02:20,490] [@script/test_logging.lua:28] [ERROR] : "this is ERROR LOG", nil, nil, nil
```

## 四、结束

---

  以上为`lua`的`logging`使用内容.