# Process

  `Process`是`cfadmin`的进程管理库, 目前在内部提供了一些进程间的通讯方法.

## 一、业务起因

### 1. 发展过程

  众所周知: `cfadmin`是基于事件驱动与协程实现的单进程服务端框架. 所以为它所适配的实际业务场景也非常简单.
  
  再通过对其对细节的优化和打磨之后, 使其在大多数项目之中都能满足性能需求. 所以这样的情况已经存了在很长时间.

  再加上生态的缺失, 让作者把绝大部分精力都放在完善内置库上. 因此对业务的态度也是: "性能满足要求, 那能跑就行".

  但是一个契机的到来, 让作者明白有些事情并不是依靠外部就能解决的.

### 2. 多核利用  

  一些使用者在这期间提出了疑问: "如何让`cfadmin`能'合理'的利用多核? '正确'的`cfadmin`部署模式是什么"?

  提出这些问题的使用者肯定已经熟读过作者写出的文档, 虽然也有一些特殊手段可以利用多核但这只是"**投机取巧**"罢了.

  然后作者就为`cfadmin`编写了多进程模式的代码. 多进程模式也是非常简单, 即: 启动`X`个进程来处理`Y`个连接请求. 

  所谓正确的部署方式就更简单了! 框架提供**命令行参数**, **手动指定进程数量**或使用`auto`参数让框架自行来判断.

  这个模式的推出确实解决了大部分使用者的难点, 其中包括作者手把手为某位前同事的业务场景更换`cfadmin`后的性能问题.

  而还有一些本来缺少架构、运维知识的同学, 在接触`cfadmin`后发出感叹: "其它框架的部署、运维也这么简单就好了".

  就在我们大家都以为事情到这里就已经结束了, 某天一位开发者的一些疑问让作者陷入选择性的难题.

### 3. 高级进阶

  试问: "使用`cfadmin`做`web`服务, 每个进程应该配置多少个数据库连接池? 是否有办法复用进程的后端连接池呢？"

  第`1`个问题看似很好回答, 但其实也是最不好回答的问题: "因为业务场景的不同, 无法保证**做什么就是一定是对的**."

  第`2`个问题看似莫名其妙, 其实也是使用者的大难题: "一旦开启的进程数量太多, 内部的空闲连接池数量也是非常的多." 

  这些看似是难题, 但其实主要还是体现在: "数据共享"、"连接复用"的问题之上; 那么我们来看下系统内置的方案是什么?

  进程间的通信主要有几种: 消息队列、共享内存、信号量等. 这些不但暴露了大量底层细节, 而且无法有效的结合事件驱动.
  
  而如果贸然封装系统级别的`API`来暴露使用, 不仅会直接影响框架运行的稳定性, 更需要为其内部去做大量复杂的设计.

  再者, 大家更加希望的是框架提供的简单、高效、易用的特性. 不仅仅只是服务于`Web`领域, 还希望有更多的业务场景.

  作者在思来想去了之后, 还是决定提供了一种**相对可靠**的跨进程通信方案.

### 4. 进程通信

  `cfadmin`所提供的解决方法其实原理也十分简单:

  1. 由`cfadmin`框架在启动期间创建`1`个`Master`进程与`N`个`Worker`进程.

  2. `cfadmin`启动之后会在`Master`进程与`Worker`进程之间主动建立`IPC`管道提供**全双工**模式的消息通信.

  3. 发送端可以将`Lua`类型参数序列化成字符串`buffer`, 接收端拿到字符串`buffer`后再反序列化为`Lua`类型.


  由于`IPC`内部并不属于**传输层**网络通信, 所以也就不需要经过完整的网络协议栈. 这也就是说明它的效率非常好.

  并且内部可以形成了一套完整的通讯闭环. 业务开发人员来也可以不必了解底层细节, 只要阅读文档了解如何使用即可.

  接下来, 我们就通过`process`库来学习如何完成进程之间的通信吧.

## 二、使用介绍

  `process`库目前内置在`cfadmin`的`lualib`目录下.

### 1. Worker 进程

  `Worker`进程可以通过`local process = require "process.worker"`来导入使用.

```lua
function process.send(...)

end
```

  使用`process.send`向`Master`进程发送消息, 此方法不会有任何返回值. 参数不能为:`function`、`userdata`、`thread`等无法序列化类型.

  注意: 此方法不会阻塞当前协程.

```lua
function process.call(...)

end
```

  使用`process.call`向`Master`进程发送消息, 此方法会在`Master`进程调用`process.ret`回应后返回. 参数不能为:`function`、`userdata`、`thread`等无法序列化类型.

  注意: 此方法阻塞当前协程, 阻塞的时间长短取决于`Master`回应的速度.

```lua
function process.on(msg_type, func)

end
```

  使用`process.on`可以注册事件, 目前`Worker`进程仅支持: `message`. 

  * `message` - `Master`进程发送的消息事件.


  注意: 此方法不会阻塞当前协程.

### 2. Master 进程

  `Master`进程可以通过`local process = require "process.master"`来导入使用.

```lua
function process.broadcast(...)

end
```

  使用`process.broadcast`方法可以向所有`worker`进程广播消息, 参数不能为:`function`、`userdata`、`thread`等无法序列化类型.

  注意: 此方法不会阻塞当前协程.

```lua
function process.ret(sessionid, ...)

end
```

  使用`process.ret`方法可以用来回应`Worker`使用`process.call`发送的消息. 参数不能为:`function`、`userdata`、`thread`等无法序列化类型.

  `process.ret`不能回应`sessionid`为`nil`的消息, 因为这类消息一般不需要回应.

  注意: 此方法不会阻塞当前协程.

```lua
function process.on(msg_type, func)

end
```

  使用`process.on`可以注册事件, 目前`Master`进程仅支持: `message`/`exit`. 

  * `exit` - 子进程退出事件

  * `message` - 子进程发送消息事件.


  注意: 此方法不会阻塞当前协程.

### 3. 一些补充

  `sessionid`是进程通信最长见到的`Integer`类型**唯一会话标识符**.

  `Worker`使用`process.call`的时候会生成, `Master`使用`process.ret`回应后应该失效(不再使用).

  `Worker`使用`process.send`的时候会置空(`nil`), 遇到`sessionid`为`nil`则表示无需回应此消息.

  `Master`使用`process.broadcast`的时候, `Worker`收到的`sessionid`也会是`nil`.

  注意: 
  
  1. 在调用`process.call`的之后请务必使用`process.ret`回应消息. 否则可能会造成**内存泄漏**.

  2. 请不要多次调用`process.ret`方法多次回应相同的`sessionid`, 这可能会造成系统异常或其它未知情况出现.

### 4. Master -> Worker 模式

  一般情况下, `Worker`进程会可以主动指定启动文件. 但如果要使用进程通信, 必须为`Master`进程创建启动文件.

  `Master`的启动文件默认路径是`script/boot.lua`, 此文件的意义相当于`Worker`的`script/main.lua`文件.

  注意: 当`Worker`的数量大于`1`的时候才能正常启动哦.

## 三、简单示例

  相信看过上述文档后有人已经迫不及待了, 现在为大家演示一些简单的场景来模拟业务.

### 1. 广播消息

  有时候我们需要广播消息到每个进程内, 让他们根据消息细节来决定分发给哪些连接.

```lua
-- script/boot.lua
local process = require "process.master"
local cf = require "cf"

cf.at(1, function ()
  process.broadcast("Hello Worker.")
end)
```

```lua
-- script/main.lua
local process = require "process.worker"

process.on('message', function (sessionid, ...)
  print(sessionid, process.pid, ...)
end)
```

  代码完成之后, 我们尝试启动4个进程来看看运行结果.

```bash
[candy@MacBookPro:~/Documents/cfadmin] $ ./cfadmin -w 4
nil 80462 Hello Worker.
nil 80464 Hello Worker.
nil 80461 Hello Worker.
nil 80463 Hello Worker.

nil 80464 Hello Worker.
nil 80463 Hello Worker.
nil 80461 Hello Worker.
nil 80462 Hello Worker.

nil 80463 Hello Worker.
nil 80461 Hello Worker.
nil 80464 Hello Worker.
nil 80462 Hello Worker.
[candy@MacBookPro:~/Documents/cfadmin] $
```

  可以看到! 结果如我们所料, 广播消息收到的也非常快.

### 2. 请求回应

  有时候我们需要像`RPC`一样发送一个请求, 并且我们希望这就像调用本地函数一样.

```lua
-- script/boot.lua
local process = require "process.master"

process.on('message', function (sessionid, pid, msg)
  print(sessionid, pid, msg)
  if sessionid then
    process.ret(sessionid, process.pid, "Thanks, this is master")
  end
end)
```

```lua
-- script/main.lua
local process = require "process.worker"

process.on('message', function (sessionid, ...)
  -- print(sessionid, process.pid, ...)
end)

print(process.call(process.pid, "Hello, this is worker-" .. process.pid))
```

```bash
[candy@MacBookPro:~/Documents/cfadmin] $ ./cfadmin -w 4
347033357516801 80800 Hello, this is worker-80800
347037652484097 80801 Hello, this is worker-80801
347041947451393 80802 Hello, this is worker-80802
347029062549505 80799 Hello, this is worker-80799
80798 Thanks, this is master
80798 Thanks, this is master
80798 Thanks, this is master
80798 Thanks, this is master
[candy@MacBookPro:~/Documents/cfadmin] $
```

### 3. 进程退出

  由于我们无法保证`Worker`进程与`Master`进程谁先执行, 所以让`Worker`进程休眠一会后再模拟退出.

```lua
-- script/boot.lua
local process = require "process.master"

process.on('exit', function (pid, rstatus)
  print(pid, rstatus, "exited")
end)
```

```lua
-- script/main.lua
require "cf".sleep(math.random())
-- os.exit(0)
os.exit(-1)
```

```bash
[candy@MacBookPro:~/Documents/cfadmin] $ ./cfadmin -w 4
81341 65280 exited
81338 65280 exited
81340 65280 exited
81339 65280 exited
[candy@MacBookPro:~/Documents/cfadmin] $
```

  `pid`表示已退出的进程`ID`, `code`表示进程的退出状态码.

## 四、最后补充

  请仔细阅读上述文档确保你的代码也同上所述行为一致, 如遇文档内未提及的内容请在`ISSUES`或**社区群**内反馈.