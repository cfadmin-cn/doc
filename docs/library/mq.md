## 一、基本介绍

---

  * `MQ`库实现了各类消息代理中间件(`Message Broker`)的连接协议, 目前支持:`redis`、`mqtt`、`stomp`协议.

  * `MQ`库基于上述协议实现了: `生产者` -> `消费者与订阅` -> `发布模型`, 可以在不依赖其它服务的情况下独立完成任务.

  * `MQ`库可以作为消费者与生产者来解决`WebApp`实例通信与`订阅`/`发布`的工作解耦, 完成多进程与跨实例通信的工作.

## 二、API学习

---

  在`MQ`库中有三种对象可供使用者自行导入. 具体的导入方法为:

  * 基于`redis`协议实现的消息队列: `local MQ = require "MQ.redis"`

  * 基于`mqtt`协议实现的消息队列:  `local MQ = require "MQ.mqtt"`

  * 基于`stomp`协议实现的消息队列: `local MQ = require "MQ.stomp"`

### 1. MQ:new

---

  函数原型: `MQ:new(opt)`

  `opt`是一个`table`类型的参数, 里面可以包含如下内容:

  * `host` - `String`类型, 消息队列的域名或者IP地址.

  * `port` - `Interge`类型, 消息队列监听的端口.

  * `auth` - `String`类型, 如果后端MQ是`Redis`, 配置了连接口令则需要配置(可选);

  * `db`   - `Integer`类型, 如果后端MQ是`Redis`,  db范围在(0-16)(可选);

  * `username` - `String`类型, 仅在`stomp`/`mqtt`协议下用作登录认证(没有可以不填写).

  * `password` - `String`类型, 仅在`stomp`/`mqtt`协议下用作登录认证(没有可以不填写).

  * `vhost` - `String`类型, 仅在使用某些特定消息队列server的时候填写(例如:rabbit).

  * `keepalive` - `Integer`类型, 仅在使用`mqtt`的时候用来出发客户端主动发出心跳包的时间.

  此方法将会创建一个的`MQ`对象实例.

### 2. MQ:on

---

  函数原型: `MQ:on(pattern, callback)`

  此方法用来订阅一个指定`pattern`. 当接收到来自`broker`的推送消息后, callback将会被调用.

  `callback`会被注入一个`table`类型的参数, 此参数将在断开连接的时候为`nil`(可用来判断服务器是否断开).

  `msg`参数会根据采用的协议的不同, 其内部包含的属性也有所不同.

### 3. MQ:emit

---

  函数原型: `MQ:emit(pattern, msg)`

  此方法用来向指定`pattern`发送消息, `msg`为`String`类型. 

  单个`MQ`可以一直复用`emit`, 内部会创建一个写入队列去完成消息的顺序发送. 

### 4. MQ:clsoe

---

  函数原型: `MQ:close()`

  此方法可以关闭不再使用的`MQ`; 在任何情况下, `MQ`对象使用完毕后都需要调用此方法来释放资源.

### 5. MQ:start

---

  函数原型: `MQ:start()`

  此方法在作为独立运行服务端时候调用.

## 三、使用示例

---

### 1. 通用消息队列

---

```lua
local MQ = require "MQ.stomp"
-- local MQ = require "MQ.redis"
-- local MQ = require "MQ.mqtt"

local cf = require "cf"
require "utils"

local mq = MQ:new { host = 'localhost', port = 6379 }

mq:on('/test', function (msg)
    print("收到来自/test的消息.")
    var_dump(msg)
end)

mq:on('/admin', function (msg)
    print("收到来自/admin的消息.")
    var_dump(msg)
end)

cf.at(0.1, function (args)
    print(mq:emit('/test', '{"code":'..math.random(1, 100)..',"from":"/test"}'))
    print(mq:emit('/admin', '{"code":'..math.random(1, 100)..',"from":"/admin"}'))
end)

mq:start()
```

### 2. Websocket订阅

---

```lua
local class = require "class"
local mq = require "MQ.redis"
local cf = require "cf"
local json = require "json"
local websocket = class("websocket")

function websocket:ctor(opt)
    self.ws = opt.ws             -- websocket对象
    self.send_masked = false     -- 掩码(默认为false, 不建议修改或者使用)
    self.max_payload_len = 65535 -- 最大有效载荷长度(默认为65535, 不建议修改或者使用)
    self.timeout = 15            -- 默认为一直等待, 非number类型会导致异常.
    self.count = 0
    self.mq = mq:new {
      host = "localhost",
      port = 6479,
      -- auth = 'admin',
      -- db = 0,
    }
end

function websocket:on_open()
    print('on_open')
    self.timer = cf.at(0.01, function ( ... ) -- 定时器
        self.count = self.count + 1
        self.ws:send(tostring(self.count))
    end)
    self.mq:on('/chat', function(msg)
      if not msg then
        return
      end
      self.ws:send(msg.payload)
    end)
end

function websocket:on_message(data, typ)
    print('on_message', self.ws, data)
    self.ws:send('welcome')
    -- self.ws:close(data)
end

function websocket:on_error(error)
    print('on_error', self.ws, error)
end

function websocket:on_close(data)
    print('on_close', self.ws, data)
    if self.timer then -- 清理定时器
      print("清理定时器")
      self.timer:stop()
      self.timer = nil
    end
    if self.mq then
      self.mq:close()
    end
end

return websocket
```

## 四、协议兼容性测试

---

  * `mqtt` - 在`emqx 3.x`上测试通过.

  * `redis` - 在`redis 3.x`以上版本测试通过.

  * `stomp` - 在`rabbitmq 3.x`上测试通过.

## 五、使用注意

---

  `MQ`库实现的`消息队列`的设计是基于`1 -> N`的, 这可能会非常适合某些基于`组播`与`广播`的应用业务场景. 

  `MQ`内部的实现是基于`任务串行化思维`的设计的, 所以每个订阅在`回调函数的返回才意味着当前任务处理完毕`.

  简单来说: 我们的`A订阅`与`B订阅`是可以被`并发调用`的. 但当前任务未处理完之前, 各自都不会处理后续的其它任务.

  