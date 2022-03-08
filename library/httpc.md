## 一、基本介绍

---

  `httpc`库框架的内部实现的http client库, 支持设置HTTP/1.1的连接、请求超时设置、头部设置;

  `httpc`库支持`http`与`https`两种协议请求, 根据传参自动判断是否需要进行安全TCP连接;

  `httpc`库封装了常见的`GET`、`POST`、`JSON`、`FILE`、`PUT`、`DELETE`请求方法, 减少开发者的工作重复程度;


## 二、基本使用
  {% raw %}
---

  导入库: `local httpc = require "httpc`"

### 1. httpc.get

---

  函数原型: `httpc.get(domain, headers, args, timeout)`

  `get`方法将会对`domain`参数发起一个`GET HTTP/1.1`请求, `timeout`参数决定请求在多久未得到回应将视为超时;

  `headers`参数为用户自定义头部, `args`参数为用户自定义请求参数并追加在`domain`的后面; 它们是`table`类型的数组;

  此方法有2个返回值, 第一个返回值是一个`Integer`类型的响应`code`或`nil`; 第二个参数是响应内容(`body`)或者`Location`;

  使用示例:

```lua
local code, body = httpc.get("http://localhost:8080/api?page=1&limit=10", {{"Auth", "admin"}})
local code, body = httpc.get("http://localhost:8080/api", {{"Auth", "admin"}}, {{'page', 1}, {'limit', 10}})
local code, body = httpc.get("http://localhost:8080/api", {{"Auth", "admin"}}, {{'page', 1}, {'limit', 10}}, 3)
-- 请求参数将会被编码为: page=1&limit=10
```

### 2. httpc.post

---

  函数原型: `httpc.post(domain, headers, args, timeout)`

  `post`方法将会对`domain`参数发起一个`POST HTTP/1.1`请求, `timeout`参数决定请求在多久未得到回应将视为超时;

  `headers`参数为用户自定义头部, `args`参数为用户自定义请求参数并被放置在`body`上; 它们是`table`类型的数组;

  此方法有2个返回值, 第一个返回值是一个`Integer`类型的响应`code`或`nil`; 第二个参数是响应内容(`body`)或者`Location`;

  此方法的`Content-Type`为`application/x-www-form-urlencoded`;

  使用示例:

```lua
local code, body = httpc.post("http://[::ffff:127.0.0.1]:8080/api", {{"Auth", "admin"}}, {{'page', 1}, {'limit', 10}}, 3)
-- 请求参数将会被编码为: page=1&limit=10
```

### 3. httpc.json

---

  函数原型: `httpc.json(domain, headers, json, timeout)`

  `json`方法将会对`domain`参数发起一个`POST HTTP/1.1`请求, `timeout`参数决定请求在多久未得到回应将视为超时;

  `headers`参数为用户自定义头部, `json`参数会被直接填充到`body`上; `json`可以是`String`或者`Table`类型(被编码);

  此方法有2个返回值, 第一个返回值是一个`Integer`类型的响应`code`或`nil`; 第二个参数是响应内容(`body`)或者`Location`;

  此方法的`Content-Type`为`application/json`;

  使用示例:

```lua
local json = requiore "json"

local code, body = httpc.json("http://localhost:8080/api", {{"Auth", "admin"}}, {page = 1, limit = 10})
-- 传递可被格式化的table参数, body为:{"page":1,"limit":10}
local code, body = httpc.json("http://localhost:8080/api", {{"Auth", "admin"}}, json.encode({page = 1, limit = 10}))
-- 传递合法的json格式化字符串, body为:{"page":1,"limit":10}
```

### 4. httpc.file

---

  函数原型: `httpc.file(domain, headers, files, timeout)`

  `file`方法将会对`domain`参数发起一个`POST HTTP/1.1`请求, `timeout`参数决定请求在多久未得到回应将视为超时;

  `headers`参数为用户自定义头部, `files`为`file`对象`{name = name, filename = filename, file = file, type = type}`数组;

  `file`对象包含名称、文件名、文件内容、文件后缀(类型), 这个请求可以保证开发者通过HTTP协议发送文件到远程服务器;

  使用示例:

```lua
local code, body = httpc.file('http://localhost:8080/view', nil, {
  {name='1', filename='1.jpg', file='1', type='abc'},
  {name='2', filename='2.jpg', file='2', type='abc'},
})
```

### 5. httpc.multi_request

---

  函数原型: `httpc.multi_request(opt)`

  `multi_request`方法为并发请求提供了可能; 在多个不相关的接口中快速获得数据, 以减少网络延迟带来的并发问题.

  `opts`是对个请求组成的一个数组, 其中的每个`item`都可能代表一个不同请求. 其中包含的参数:

  * `domain`  - `String`类型, 此参数为用户请求的域名与路径;

  * `method`  - `String`类型, 此参数为用户请求的的方法名称; 包括: `["get", "post", "json", "file"]`;

  * `headers` - `table[]`类型, 此参数为用户请求头部信息数组; 示例: `{{"Token", "932njkaad"}, {"signature", "21321"}}`

  * `args` - `table[]`类型, 此参数为用户请求参数信息数组; (`get`请求特有);

  * `body` - `table[]`类型, 此参数为用户请求内容信息数组; (`post`请求特有);

  * `json` - `table[]`类型, 此参数为用户请求内容信息数组; (`json`请求特有);

  * `file` - `table[]`类型, 此参数为用户请求文件信息数组; (`file`请求特有);

  使用示例:

```lua
local httpc = require "httpc"
local LOG = require "logging"

local ok, response = httpc.multi_request {
  {
    domain = "http://localhost:8080/api",
    method = "get",
    headers = {{"Auth", "admin"}},
    args = {{'page', 1}, {'limit', 10}}
  },

  {
    domain = "http://localhost:8080/api",
    method = "post",
    headers = {{"Auth", "admin"}},
    body = {{'page', 1}, {'limit', 10}}
  },

  {
    domain = "http://localhost:8080/api",
    method = "json",
    headers = {{"Auth", "admin"}},
    json = json.encode({page=1, limit=10})
  },

  {
    domain = "http://localhost:8080/api",
    method = "file",
    headers = {{"Auth", "admin"}},
    files = {
      {name='1', filename='1.jpg', file='1', type='abc'},
      {name='2', filename='2.jpg', file='2', type='abc'},
    }
  }

}

LOG:DEBUG(response, "回应数量: " .. #response)
```
  {% endraw %}
## 三、请求对象

---

  什么是HTTP请求对象? HTTP请求对象提供了连接复用的功能, 在对相同域名的不同方法请求可以无需重新建立TCP连接.

### 1. 使用差异

---

  前面章节所述的`httpc`库都会在连接请求完成(无论成功与否), 都将释放连接并返回结果到开发者调用端. 这样通常是没有问题的.

  但是某些特殊场景下! 我们需要在整个流程中对一个远程服务同时调用多次的时候, 这样前面所述的方式就会较浪费资源.

  而`HTTP请求对象`是基于`class`建立的请求抽象模型; 所以它可以非常简单就能保留链接引用下文;

  其内部所有实现方法与`httpc`库是一样的, 这样就可以在不暴露内部接口的同事提供连接复用的功能.

### 2. 使用方式

---

  由于`HTTP请求对象`是基于对象实现, 其使用相关方法的时候需要使用冒号(`:`)来调用;

  `HTTP请求对象`使用之初需要先导入`httpc.class`对象, 然后使用前面所述的方法来进行使用;

### 3. 使用示例

---

```lua
local httpc = require "httpc.class"
local hc = httpc:new {}

local code, data = hc:get("localhost:8080/api?a=1&b=2")
print(code, data)
hc:close()
```

## 四、代理请求

---

  在`特定的网络`环境下, 我们可能会需要用到第三方的服务器来完成网络连接. 这就是通常意义上的`代理请求`.

  `httpc`库目前支持两种协议完成代理: 1. 基于HTTP的`Connect`协议请求代理, 2. 基于`sock5`的请求转发代理;

  在使用之前, 我们先要导入基于`http`的`Proxy`库: `local http_proxy = require "httpc.proxy"`

### 1. 基于Connect的代理

---

  函数原型: `http_proxy.tunnel_connect(opt)`

  此方法通过使用HTTP的`Connect`方法来完成认证与代理; `opt`参数是一个配置用的`table`, 内部包含以下参数:

  * `opt.proxy_domain` - `String`类型的代理域名与端口(可选), 例如: `http://proxy.com/`, `http://proxy.com:8080/`;

  * `opt.source_domain` - `String`类型的目的域名与端口(可选), 例如: `http://proxy.com/`, `http://proxy.com:8080/`;

  * `opt.headers` - `table`类型数组参数表示需要为通道握手添加额外的头部信息, 这在一些特殊的代理服务器中可能会用到;

  * `opt.auth` - `table`类型参数; 如有需要, 可配置此字段内的`username`与`password`属性使用`basic`认证;

  * `opt.timeout` - `Number`类型参数表示与代理服务器之间握手的超时时间, 超过这个时间将会主动断开连接;

  此方法在代理建立成功后将会返回一个`httpc class`对象, 用户可以使用此对象来访问服务器的. 其他情况会返回`nil`与出错信息;

  当您不想时候`http proxy`的时候, 请主动调用它的`close`方法回收资源;


### 2. 基于Sock5的代理

---

  函数原型: `http_proxy.socks5_connect(opt)`

  此方法通过使用`SOCK5`协议来完成认证与代理; `opt`参数是一个配置用的`table`, 内部包含以下参数:

  * `opt.proxy_domain` - `String`类型的代理域名与端口(可选), 表示方式为: `socks5://ip|domain:port`

  * `opt.source_domain` - `String`类型的目的域名与端口(可选), 例如: `http://proxy.com/`, `http://proxy.com:8080/`;

  * `opt.auth` - `table`类型参数; 如有需要, 可配置此字段内的`username`与`password`属性使用`用户名/密码`认证;

  * `opt.timeout` - `Number`类型参数表示与代理服务器之间握手的超时时间, 超过这个时间将会主动断开连接;

  此方法在代理建立成功后将会返回一个`httpc class`对象, 用户可以使用此对象来访问服务器的. 其他情况会返回`nil`与出错信息;

  当您不想时候`http proxy`的时候, 请主动调用它的`close`方法回收资源;


## 五、使用注意

---

  * `httpc`的任何一个方法都不会为用户解析`response body`;

  * `httpc`的遇到`301`/`302`的时候会返回`状态码`与`Location`, 不会理会`response body`(因为没有意义);

  * `httpc`每次都会随机生成一个`User-Agent`, 这个会在框架内部自动完成;


## 六、结束

---

  以上为`lua`的`httpc`使用内容.