## 一、基本介绍

---

  `Cache`封装自`Redis 2.0`协议实现的客户端连接库;

  `Cache`提供Redis断线重连、命令重试、连接池等特性;

  在使用之前, 请先确保已经导入库: `local Cache = require "Cache"`.

### 1. Cache:new

---

  函数原型: `Cache:new(opt)`

  `opt`参数是一个`lua table`. 请根据实际情况传入如下字段:

  * `host` - `String`类型; 连接的`主机名`或`IP地址`.

  * `port` - `Integer`类型; 连接的主机`端口号`.

  * `auth` - `String`类型; 设置的`口令`(没有口令可以为`nil`).

  * `db` - `Integer`类型; 设置`Redis`的实例.

  * `max` - `Integer`类型; 最大连接池大小.

  此方法返回一个新创建的`cache`对象.

### 2. Cache:connect

---

  函数原型: `Cache:connect()`

  调用此方法开始连接Redis. 连接成功返回True, 否则将会`持续连接`并且输出`连接失败原因`.

## 二、支持的方法

---

  目前`Cache`用特殊的封装实现支持了90%的方法, 有兴趣的朋友可以进行参考下面的示例使用.

## 三、测试的示例

---

```lua
-- script/main.lua
local Cache = require "Cache"
local cf = require "cf"
local Log = require("logging"):new()

local opt = {
  host = "localhost",
  port = 6379,
  auth = nil,
  db = 1,
  max = 1,
}

cf.fork(function ( ... )
  local Cache = Cache:new(opt)
  local ok, err = Cache:connect()
  if not ok then
      return print(err)
  end
  -- 测试 GET/SET/DEL 命令示例
  local ok, ret = Cache:set("test", 1)
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:get("test")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:del("test")
  Log:DEBUG(ok, ret)

  -- 测试哈希表命令示例
  local ok, ret = Cache:hmset("website", "google", "www.google.com", "baidu", "www.baidu.com")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:hlen("website")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:hkeys("website")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:hgetall("website")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:hmget("website", "google", "baidu")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:hdel("website", "google", "baidu")
  Log:DEBUG(ok, ret)

  -- 测试列表命令示例
  local ok, ret = Cache:lpush("language", "lua", "python", "C", "C++")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:rpush("language", "golang", "java", "ruby", "javascript")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:lrange("language", 0, -1)
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:ltrim("language" , -1, 0)
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:llen("language")
  Log:DEBUG(ok, ret)

  -- 测试有序集合命令示例
  local ok, ret = Cache:smembers("bbs")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:sadd("bbs", "discuz.cn", "group.google.com", "oschina.net", "csdn.net")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:sismember("bbs", "oschina.net")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:srem("bbs", "discuz.cn", "group.google.com", "oschina.net", "csdn.net")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:sismember("bbs", "oschina.net")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:sadd("book1", "宝宝的C++", "宝宝的HTML", "宝宝的CSS", "C程序设计")
  local ok, ret = Cache:sadd("book2", "宝宝的C++", "宝宝的HTML", "宝宝的CSS", "C++从入门到放弃")
  local ok, ret = Cache:sadd("book3", "宝宝的C++", "宝宝的HTML", "宝宝的CSS", "MySQL从入门到删库跑路")

  local ok, ret = Cache:sdiff("book1", "book2", "book3")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:del("book1", "book2", "book3")
  Log:DEBUG(ok, ret)

  -- 测试有序集合命令示例
  local ok, ret = Cache:zadd("scores", 10, "admin", 20, "Candy", 30, "QQ", 40, "Guest")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:zrange("scores", 0, -1)
  -- local ok, ret = Cache:zrange("scores", 0, -1, "WITHSCORES")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:zcount("scores", 10, 100)
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:zscore("scores", "QQ")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:zrank("scores", "Candy")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:zrem("scores", "admin", "Candy", "QQ", "Guest")
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:del("scores")
  Log:DEBUG(ok, ret)

  -- 脚本支持
  local ok, ret = Cache:script_load("return 10086")
  Log:DEBUG(ok, ret)

  local sha = ret
  local ok, ret = Cache:script_exists(sha)
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:evalsha(sha, 0)
  Log:DEBUG(ok, ret)

  local ok, ret = Cache:script_flush()
  Log:DEBUG(ok, ret)

  -- 其它一些特殊方法支持
  -- type, move, rename, keys, randomkey等等
  Log:DEBUG(Cache:count())

  -- 管道命令支持
  local ok, ret = Cache:pipeline {
    {"HMSET", "USER_INFO", "name", "Candy", "email", '869646063@qq.com', 'phone', '13000000000'},
    {"HGET",  "USER_INFO", "email"},
    {"HGET",  "USER_INFO", "phone"},
  }
  Log:DEBUG(ok, ret)
end)
```

  命令行输出结果:

```bash
[candy@MacBookPro:~/cfadmin] $ ./cfadmin -e script/test_Cache.lua
[2020-09-07 23:19:19,142] [@script/test_Cache.lua:22] [DEBUG] : true, "OK"
[2020-09-07 23:19:19,142] [@script/test_Cache.lua:25] [DEBUG] : true, "1"
[2020-09-07 23:19:19,143] [@script/test_Cache.lua:28] [DEBUG] : true, 1
[2020-09-07 23:19:19,144] [@script/test_Cache.lua:32] [DEBUG] : true, "OK"
[2020-09-07 23:19:19,144] [@script/test_Cache.lua:35] [DEBUG] : true, 2
[2020-09-07 23:19:19,144] [@script/test_Cache.lua:38] [DEBUG] : true, {[1]="google", [2]="baidu"}
[2020-09-07 23:19:19,145] [@script/test_Cache.lua:41] [DEBUG] : true, {[1]="google", [2]="www.google.com", [3]="baidu", [4]="www.baidu.com"}
[2020-09-07 23:19:19,145] [@script/test_Cache.lua:44] [DEBUG] : true, {[1]="www.google.com", [2]="www.baidu.com"}
[2020-09-07 23:19:19,145] [@script/test_Cache.lua:47] [DEBUG] : true, 2
[2020-09-07 23:19:19,145] [@script/test_Cache.lua:51] [DEBUG] : true, 4
[2020-09-07 23:19:19,146] [@script/test_Cache.lua:54] [DEBUG] : true, 8
[2020-09-07 23:19:19,146] [@script/test_Cache.lua:57] [DEBUG] : true, {[1]="C++", [2]="C", [3]="python", [4]="lua", [5]="golang", [6]="java", [7]="ruby", [8]="javascript"}
[2020-09-07 23:19:19,146] [@script/test_Cache.lua:60] [DEBUG] : true, "OK"
[2020-09-07 23:19:19,146] [@script/test_Cache.lua:63] [DEBUG] : true, 0
[2020-09-07 23:19:19,146] [@script/test_Cache.lua:67] [DEBUG] : true, {}
[2020-09-07 23:19:19,146] [@script/test_Cache.lua:70] [DEBUG] : true, 4
[2020-09-07 23:19:19,146] [@script/test_Cache.lua:73] [DEBUG] : true, true
[2020-09-07 23:19:19,147] [@script/test_Cache.lua:76] [DEBUG] : true, 4
[2020-09-07 23:19:19,147] [@script/test_Cache.lua:79] [DEBUG] : true, false
[2020-09-07 23:19:19,147] [@script/test_Cache.lua:86] [DEBUG] : true, {[1]="C程序设计"}
[2020-09-07 23:19:19,147] [@script/test_Cache.lua:89] [DEBUG] : true, 3
[2020-09-07 23:19:19,148] [@script/test_Cache.lua:93] [DEBUG] : true, 4
[2020-09-07 23:19:19,148] [@script/test_Cache.lua:97] [DEBUG] : true, {[1]="admin", [2]="Candy", [3]="QQ", [4]="Guest"}
[2020-09-07 23:19:19,148] [@script/test_Cache.lua:100] [DEBUG] : true, 4
[2020-09-07 23:19:19,148] [@script/test_Cache.lua:103] [DEBUG] : true, "30"
[2020-09-07 23:19:19,148] [@script/test_Cache.lua:106] [DEBUG] : true, 1
[2020-09-07 23:19:19,148] [@script/test_Cache.lua:109] [DEBUG] : true, 4
[2020-09-07 23:19:19,148] [@script/test_Cache.lua:112] [DEBUG] : true, 0
[2020-09-07 23:19:19,150] [@script/test_Cache.lua:116] [DEBUG] : true, "a9b6689bf0d2962188a5fb8f2502e8de5a19fc26"
[2020-09-07 23:19:19,150] [@script/test_Cache.lua:120] [DEBUG] : true, {[1]=1}
[2020-09-07 23:19:19,151] [@script/test_Cache.lua:123] [DEBUG] : true, 10086
[2020-09-07 23:19:19,152] [@script/test_Cache.lua:126] [DEBUG] : true, "OK"
[2020-09-07 23:19:19,152] [@script/test_Cache.lua:130] [DEBUG] : 1, 1
[2020-09-07 23:19:19,153] [@script/test_Cache.lua:138] [DEBUG] : true, {[1]={[1]=true, [2]="OK"}, [2]={[1]=true, [2]="869646063@qq.com"}, [3]={[1]=true, [2]="13000000000"}}
[candy@MacBookPro:~/Documents/cfadmin] $
```