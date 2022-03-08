## 起源

---

  数据库技术跟我们的日常生活息息相关! 无论是好友在线实时沟通、在线观看试试新闻资讯都无不与数据技术相挂钩. 例如: 我们使用的应用随即向微信的服务器发送了一个网络请求，这时服务器从数据库获取请求结果然后发送回我们的手机.

  计算机数据库技术的革新也会彻底改变我们的生活方式。假设现在人类使用的数据库技术仍然非常落后, 网络支付一次需要花上十几分钟. 那网络支付一定不会像今天这样普及，然后网上商城也一定不会像今天一样方便。

  如果看个网页，需要花上几分钟，那么更别说看直播或是在视频网站上看电视剧了,我们会选择看电视而不是打开电脑。而这些，恰恰是我们十年前过着的生活,即使变革的很大部分原因在于网络带宽以及速度的提升，但是数据库技术的革新的功劳也不可忽视的。

## 支持

---

  `Lua`生态中有一个叫`LuaSQL`的库已经完成了一些常见数据库的驱动对接, 但是调研后发现它并不能满足框架的运行时要求.

  所以为了更加`"贴合框架运行时、独有开发模型"`的设计目的, 我们必须根据市面上的产品发布的技术文档来进行研发.

## 介绍

---

  目前已经实现的`DB`库支持三种数据库驱动包括: `MySQL`、`SQL Server`、` PostgreSQL`, 足以应付大部分开发者对数据库的选择.

  由于内部协议完全是基于框架实现, 所以不用担心编译框架的时候需要增加外部依赖. 这也无形中增加了框架可维护性.

  同时我们还可以根据协议, 事先一些`ORM`、`ADO`、`ODBC`无法提供的功能; 这就需要大家在实际使用中去体会了 :)

### 一、方法介绍

---

  由于我们有多种不同的驱动, 所以我们可以使用下面的方法导入需要的驱动:

  * `local DB = require "DB.mssql"`

  * `local DB = require "DB.pgsql"`

  * `local DB = require "DB.mysql"`

  注意: 如果使用`MySQL`驱动的话, 可以直接使用`local DB = require "DB"`. 因为我们默认`MySQL`为常见的数据库.

#### DB:new

---

  函数原型: `DB:new(opt)`

  `opt`参数是一个`lua table`. 请根据实际情况传入如下字段:

  * `host` - `String`类型; 表示需要连接的`主机名`或`IP地址`;

  * `port` - `Integer`类型; 表示需要连接的`端口号`;

  * `username` - `String`类型; 表示连接的数据库`账号`;

  * `password` - `String`类型; 表示连接的数据库`密码`;

  * `database` - `String`类型; 这个参数表示`默认数据库`;

  * `charset` - `String`类型; 这个参数表示`默认字符集`;

  * `max` - `Integer`类型; 表示连接池的`最大连接数量`;

  此方法在创建成功后, 返回值为一个`db`对象.

#### DB:connect

---

  函数原型: `DB:connect()`

  调用此方法开始连接`mssql`. 连接成功返回`True`, 否则将会持续进行连接并且输出`连接失败原因`.

#### DB:query

---

  函数原型: `DB:query(sql_string)`

  `DB`连接成功后即可使用; 否则会提示使用者先初始化. 

  `sql_string`是一个`String`类型的的一个标准`SQL`语句, 并且利用`[[]]`可以支持多行表示法;

  如果`sql_string`执行失败, 则会返回一个`nil`与`string`类型的字符串来解释出错原因.


### 二、使用指南

---

#### 1.1 MYSQL 使用说明

---

  框架基于`MySQL 4.1`协议实现的`MySQL`客户端库, 主要提供断线重连、连接池等特性;

  理论上本协议支持: `MySQL 5.1`与更新的版本, 请开发者在支持的版本内合理使用.

#### 1.2 MYSQL 使用示例

---

```lua
local LOG = require "logging"
local cf = require "cf"
local DB = require "DB"

local db = DB:new {
  host = 'localhost',
  port = 3306,
  database = 'mysql',
  charset = 'utf8mb4',
  username = 'root',
  password = '123456789',
  max = 1,
}

local ok = db:connect()
if not ok then
  return LOG:DEBUG("连接mysql失败")
end
LOG:DEBUG("连接成功")

local info, err = db:query([[SELECT 1.0 AS Version, '2' AS Title, 3 AS People]])
if not info then
  return print(info, err)
end
LOG:DEBUG(info)
```


#### 2.1 PGSQL 使用说明

---

  框架基于`PGSQL 3.0`协议实现的`PostgreSQL`客户端库, 主要提供断线重连、连接池、数据结构解析等特性;

  理论上本协议支持: `PostgreSQL 7.4`与更新的版本, 请开发者在支持的版本内合理使用.

#### 2.2 PGSQL 使用示例

---

```lua
require "utils"

local DB = require "DB.mssql"

local db = DB:new {
  host = 'localhost',
  port = 5432,
  charset = 'UTF-8',
  database = 'postgres',
  username = 'postgres',
  password = '123456789..',
  max = 1,
}

local ok, err = db:connect()
if not ok then
  return print(ok, err)
end

local info, err = db:query([[SELECT 1.0 AS Version, '2' AS Title, 3 AS People]])
if not info then
  return print(info, err)
end
var_dump(info)
```


#### 3.1 MSSQL 使用说明

---

  框架基于`TDS 7.1.1`协议实现的`MSSQL`客户端库, 主要提供断线重连、连接池、数据结构解析、数据自动分包等特性;

  理论上本协议支持: `SQL Server 2000 SP2`与更新的版本, 请开发者在支持的版本内合理使用.


#### 3.2 MSSQL 使用示例

---

```lua
require "utils"

local DB = require "DB.mssql"

local db = DB:new {
  host = 'localhost',
  port = 1433,
  database = 'master',
  username = 'sa',
  password = '123456789..',
  max = 1,
}

local ok, err = db:connect()
if not ok then
  return print(ok, err)
end

local info, err = db:query([[SELECT 1.0 AS Version, '2' AS Title, 3 AS People]])
if not info then
  return print(info, err)
end
var_dump(info)
```

### 三、 事务

---

  `事务(Transaction)`是操作数据库的程序执行单元(`unit`)、并发控制的基本单位. 它通常是由开发者自定义编写的逻辑;

  `事务`的特性包括: 原子性（`atomicity`）、一致性（`consistency`）、隔离性（`isolation`）、持久性（`durability`）;

  `事务`是不可分割的操作序列. 在每一次事务结束时, 数据库使用`事务的特性`维护数据库的`完整性`并保持所有数据的一致性;

#### 1. 使用API

---

  函数原型: `DB:transaction(function(session) end)`

---

  为了让`Lua`也能使用事务完成数据库操作, 那就必须在`DB`库内为`每一种数据库驱动`都适配了一个统一`事务接口`.

  `transaction`方法需要传递一个`function`作为参数, 开发者可以在此`function`内部自定义基于事务的业务逻辑.

  传入`transaction`内的回调会被注入一个`session`参数, 这个`session`就是一个已开启事务的`database`连接.

  注入内部的`session`参数包含: `session:query(sql)`、`session:rollback()`、`session:commit()`三个方法.

  `query`方法用来执行`CRUD`操作, 其效果等同于之前介绍的`DB:query(sql)`. 区别在于这个是独享的数据库连接.

  `rollback`与`commit`方法对应数据库的`提交`与`回滚`关键字含义, 在`PGSQL`是`COMMIT`, 在`MSSQL`内是`COMMIT TRAN`;

  `DB:transaction`的返回值: 只有在事务提交成功`commit`才会返回`true`, 执行失败、连接异常都会返回`false`;

#### 2. 使用示例

--- 

```SQL
-- MySQL 测试数据表
CREATE TABLE IF NOT EXISTS `test`.`test` (
  `id` int unsigned NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

```lua
-- MySQL 示例代码
local LOG = require "logging"

local DB = require "DB.mysql"

local db = DB:new {
  host = "localhost",
  port = 3306,
  database = "test",
  username = "root",
  password = "123456789",
}

LOG:DEBUG(db:connect())

local ok, err = db:transaction(function ( session )
  LOG:DEBUG(session:query([[INSERT INTO `test`.`test` VALUE(1)]]))
  LOG:DEBUG(session:query([[INSERT INTO `test`.`test` VALUE(2)]]))
  LOG:DEBUG(session:query([[INSERT INTO `test`.`test` VALUE(3)]]))
  -- return session:rollback() -- 回滚更改
  return session:commit()   -- 提交更改
end)
LOG:DEBUG(ok, err)

LOG:DEBUG(db:query([[SELECT * FROM `test`.`test`]]))
```

---

```sql
-- PGSQL 测试数据表
CREATE TABLE IF NOT EXISTS "public"."test" (
  "id" int4 NOT NULL,
  PRIMARY KEY ("id")
);
```

```lua
-- PGSQL 示例代码
local LOG = require "logging"

local DB = require "DB.pgsql"

local db = DB:new {
  host = "localhost",
  port = 5432,
  database = "postgres",
  username = "postgres",
  password = "123456789",
}

LOG:DEBUG(db:connect())

local ok, err = db:transaction(function ( session )
  LOG:DEBUG(session:query([[INSERT INTO "public"."test" VALUES (1)]]))
  LOG:DEBUG(session:query([[INSERT INTO "public"."test" VALUES (2)]]))
  LOG:DEBUG(session:query([[INSERT INTO "public"."test" VALUES (3)]]))
  -- return session:rollback() -- 回滚更改
  return session:commit()   -- 提交更改
end)
LOG:DEBUG(ok, err)

LOG:DEBUG(db:query([[SELECT * FROM "public"."test"]]))
```

---

```sql
-- 数据库创建语句
CREATE DATABASE [test]
ON
PRIMARY
(
  NAME = N'test',
  FILENAME = N'/var/opt/mssql/data\test.ndf',
  SIZE = 8MB,
  MAXSIZE = UNLIMITED,
  FILEGROWTH = 64MB
)
LOG ON
(
  NAME = N'test_log',
  FILENAME = N'/var/opt/mssql/data\test_log.ldf',
  SIZE = 8MB,
  MAXSIZE = UNLIMITED,
  FILEGROWTH = 64MB
)
GO

ALTER DATABASE [test] COLLATE Chinese_PRC_90_CS_AS_KS_SC
GO

EXEC [test].dbo.sp_changedbowner N'sa'

-- 数据表创建语句
CREATE TABLE [dbo].[test] (
  [id] int NOT NULL,
  PRIMARY KEY CLUSTERED ([id])
WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)
)
```

```lua
-- MSSQL 示例代码
local LOG = require "logging"

local DB = require "DB.mssql"

local db = DB:new {
  host = "localhost",
  port = 1433,
  database = "test",
  username = "sa",
  password = "123456789",
}

LOG:DEBUG(db:connect())

local ok, err = db:transaction(function ( session )
  LOG:DEBUG(session:query([[INSERT INTO [dbo].[test] VALUES (1)]]))
  LOG:DEBUG(session:query([[INSERT INTO [dbo].[test] VALUES (2)]]))
  LOG:DEBUG(session:query([[INSERT INTO [dbo].[test] VALUES (3)]]))
  -- return session:rollback() -- 回滚更改
  return session:commit()   -- 提交更改
end)
LOG:DEBUG(ok, err)

LOG:DEBUG(db:query("SELECT * FROM [dbo].[test]"))
```

#### 3. 使用注意

---

  由于不同数据库的事务API实现与行为差异, 所以大家不能假设API的行为可能就是数据库的行为.

  在使用框架支持的数据库事务之前务必进行详细的测试, 这样能确保你是否知晓事务的实现行为与你的业务是一致的.

  1. 框架内部虽然能捕捉异常并且自动回滚, 但还是要求开发者请不要自行在事务回调内部抛出异常;

  2. 请不要在事务执行回调内做任何耗时操作, 这可能会`对高并发程序造成意想不到的性能瓶颈`;

  3. 任何情况下`请不要自定义返回值`, 因为对框架来说`这样的行为是未知的`. `session`的可用的行为框架都已封装好;

  4. 所有事务以`return session:rollback()`或`return session:commit()`结束. 否则框架能自动识别抛出异常.





  