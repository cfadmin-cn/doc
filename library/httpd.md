## 一、基本介绍

---

  `httpd`库是一个`web`服务器套件, 基于`HTTP/1.1`协议实现而来; 内置了高性能的http协议解析器与urldecode解析库.

  `httpd`库在内部封装了标准路由处理能力, 也提供了一套`静态文件`读取能力, 这样能为使用者提供全方位的开发套件服务;

  `httpd`库具有高效的短连接快速响应能力与海量长连接保持能力; 内部的`Websocket`实现基于`RFC-6544`与`RFC-7692`;

## 二、基础API

---

  使用之前需要先导入`httpd`库, `local httpd = require "httpd"`

### 1. httpd:new

---

  函数原型: `httpd:new()`

  使用`new`方法创建一个`httpd`对象, 此方法返回一个默认的`httpd`对象;

### 2. httpd:max_path_size

---

  函数原型: `httpd:max_path_size(size)`

  将参数`size`的值设置`path`的最大长度, 实际请求来临的时候超过这个值`httpd`将会返回`414`. (默认值为: 1024)

  此类型方法不会有任何返回值;

### 3. httpd:max_header_size

---

  函数原型: `httpd:max_header_size(size)`

  将参数`size`的值设置`header`的最大长度, 实际请求来临的时候超过这个值`httpd`将会返回`431`. (默认值为: 65535)

  此类型方法不会有任何返回值;

### 4. httpd:max_body_size

---

  函数原型: `httpd:max_body_size(size)`

  将参数`size`的值设置`body`的最大长度, 实际请求来临的时候超过这个值`httpd`将会返回`413`. (默认值为: 1024 * 1024)

  此类型方法不会有任何返回值;

### 5. httpd:enable_cookie

---

  函数原型: `httpd:enable_cookie()`

  此方法将会开启`use`路由的`cookie`解析, 默认情况下是关闭的. 此方法的作用是为了让`adimin`库可以正常使用;  

  如果您在使用`admin`库的时候无法`成功登陆`, 请尝试调用此方法;

  此类型方法不会有任何返回值;

### 6. httpd:cookie_secure

---

  函数原型: `httpd:cookie_secure(secure)`

  此方法设置cookie密匙, 设置之前请先开启`enable_cookie`;

  `secure`参数推荐设置一个复杂的值(至少`10-20`个字符), 因为它决定了你的`cookie`内容是否安全;

  此类型方法不会有任何返回值;

### 7. httpd:before

---

  函数原型: `httpd:before(function)`

  此方法注册了一个`function`参数过滤器; 参数`function`是一个回调函数, 所有的`API`与`USE`路由之前会先调用它;

  此类型方法不会有任何返回值;

  
### 8.  httpd:api

---

  函数原型: `httpd:api(route, apiHandle)`

  此方法为`route`所在的路由注入回调函数, `apiHandle`即可以是一个函数或者`lua table(class)`(很少用).

  此类型方法不会有任何返回值;

### 9.  httpd:use

---

  函数原型: `httpd:use(route, useHandle)`

  此方法为`route`所在的路由注入回调函数, `useHandle`即可以是一个函数或者`lua table(class)`(很少用).

  此类型方法不会有任何返回值;

### 10. httpd:ws

---

  函数原型: `httpd:ws(route, wsHandle)`

  此方法为`route`所在的路由注入`Websocket`协议回调`类`, 内部会自动完成`HTTP/1.1` -> `Websocket`的协议升级;

  在客户端连接成功完成协议升级后, 会触发`ctor` -> `on_open` 的方法调用; 当最后的`on_open`方法被调用后握手流程完成;

  在上面提到的方法中, 请不要做任何`阻塞`或者`同步非阻塞`调用; 否则在大量频繁连接与销毁场景下会造成连接缓慢等问题;

  此类型方法不会有任何返回值;

### 11. httpd:static

---

  函数原型: `httpd:static(folder, ttl)`

  `folder`参数定义了静态文件路由的`查找目录`, `ttl`参数为静态文件是否有缓存周期. 默认情况下未开启`static`路由;

  `ttl`参数默认为永不缓存静态文件; 在开发者手动修改`ttl`后, 浏览器再次访问相同的资源时会优先从本地读取.

  此类型方法不会有任何返回值;

### 12. httpd:listen

---

  函数原型: `httpd:listen(ip, port, backlog)`

  `listen`方法用于告诉httpd对象监听指定端口, `ip`指定为"0.0.0.0"即可, `port`则为你想监听的端口号.

  合理的设置`backlog`能减少连接被重置的情况(默认值为`128`).

  此类型方法不会有任何返回值;

### 13. httpd:listen_ssl

---

  函数原型: `httpd:listen_ssl(ip, port, backlog, key, cert, pw)`

  `listen_ssl`方法用于告诉httpd对象监听指定端口, `ip`指定为"0.0.0.0"即可, `port`则为你想监听的端口号. 

  `key`与`cert`指定私钥与证书所在的文件路径, 不配套的证书与私钥怎会抛出异常. 如果证书有密码则需要配置`pw`.

  合理的设置`backlog`能减少连接被重置的情况(默认值为`128`).

    
### 14. httpd:listenx

---

  函数原型: `httpd:listenx(unix_domain_path, backlog)`

  `listenx`方法用于告诉httpd对象监听指定`unix domain socket`(本地套接字).

  合理的设置`backlog`能减少连接被重置的情况(默认值为`128`).

### 15. httpd:enable_rest

---

  函数原型: `httpd:enable_rest()`

  调用后`httpd`将会开启`rest语法`支持, 所有`rest语法`的匹配到的`参数`将会放在`HTTP Content`的`query`属性上.

  基本语法如下所示:

```lua

--- 语法①
app:api("/article/{number:id}.html", function(content)
  --- TODO
end)

--- 语法②
app:api("/article/{string:id}.html", function(content)
  --- TODO
end)

--- 语法③
app:api("/article/{id}.html", function(content)
  --- TODO
end)

--- 语法④
app:api("/nlist/{number[]:nlist}", function(content)
  --- TODO
end)

--- 语法⑤
app:api("/slist/{string[]:slist}", function(content)
  --- TODO
end)

```

  语法①的`id`为`Number`类型, 如果你需要的是`Integer`. 那么请自行进行转换.

  语法②与③的`id`为`String`类型, 它可以是任何合理的`字符串`. 语法③只是语法②的一种简写.

  语法④与⑤的`nlist`与`slist`均为`table`类型, 内部会自动转换成`指定类型数组`.

  <b>注意</b>:

  * 不建议`大规模`使用`rest语法`(性能一般), 如无`特殊情况`请不要开启支持;

  * 不建议使用④与⑤语法, 因为并不知道客户端传来的数据是否`"合理"`;

### 16. httpd:log

---

  函数原型: `httpd:log(filename)`

  `log`方法用来设置日志需要记录`filename`文件内, 默认情况下框架不会讲日志写入到磁盘文件内;

### 17. httpd:nolog

---

  函数原型: `httpd:nolog(disable)`

  `nolog`方法用来关闭`httpd`库除启动日志外的所有日志信息, 这可能会提升`httpd`库的`整体请求处理效率`;

### 18. httpd:enable_gzip

---

  函数原型: `httpd:enable_gzip()`

  `enable_gzip`方法用来开启框架响应请求的时候, 是否需要对响应内容进行压缩; 默认情况下是关闭的;

  目前框架支持两种响应压缩算法: `gzip`、`deflate`;

### 19. httpd:enable_error_pages

---

  函数原型: `httpd:enable_error_pages()`

  `enable_error_pages`方法用来开启框架响应错误的时候应该使用内部的错误页面; 默认情况下在发生错误的时候不会有任何内容;

  开启后会使用内部的一些页面来浏览器中提示用户响应的错误码, 例如: `500`, `505`, `413`, `431`等等;


### 20. httpd:enable_cros

---

  函数原型: `httpd:enable_cros(timeout)`

  `enable_cros`用来开启跨域支持, 但是默认情况下是被关闭的; 

  当框架前置有网关和负载均衡器时, 应该在网关上设置以便统一管理; 提供这种配置的原因是可以方便开发和独立运行的时候使用;

  因为框架对跨域配置支持有限, 开启跨域后造成的安全限制大大降低; 除非有必要, 否则并不建议在框架层配置;



## 三、日志格式

---

```bash
[年/月/日 时:分:秒] - [ip] - [x-real-ip] - [path] - [method] - [http code] - [request timeline]
```

## 四、中间件

---

  中间件在定义了软件(框架)层与业务层之间的一系列行为, 提供了一种`非入侵`的一种方式来实现对处理流程的`改造`;

  `httpd`库为了实现中间件设计模式而提供了`httpd:before`方法, 使用者可以`根据实际情况`来决定是否使用此方法;


## 五、请求内容

---

  每个`http请求`来临的时候都会在调用`路由回调`时为其传入一个`Content`, 这个`Content`里包含了请求相关的一些内容.

### 1.args属性

---

  当客户端发送的请求包含以下情况的时候, `Content`参数会包含`args`属性, 例如:

  * 使用`GET`方法发出请求, 并且`url`后缀带有`?a=1`或`?a=1&b=2`的查询请求.

  * 使用`POST`方法发出请求, 并且`body`不为空且带有`?a=1`或`?a=1&b=2`的查询请求.(`Content-Type`必须为`application/x-www-form-urlencoded`)

### 2.header属性

---

  当客户端的请求来临的时候, 此属性会包含所有请求所携带头部信息. 不接受空的头部信息.


### 3.body属性

---

  `body`属性会在多种情况下出现, 并且内容由不同请求类型决定. 例如: 请求方法、请求体类型;

  下面我们来看下不同请求方法中`body`的不同 :

  * 当请求方法为`GET`的时候, `body`始终为`nil`(不存在);

  * 当请求方法为`POST`并且存在`Content.xml`或`Content.json`任何一种属性的时候`body`会存在(但是不会被框架解析);

  * 当请求方法为`PUT`的时候`body`可能会存在, 但是不会有任何意义上的标志表示`body`可能是某种类型;

### 4. files

---

  当客户端使用`multipart/form-data`传递数据到服务端时, 请求头部中将会有这个属性; 此属性专门用来存储上传文件的内容;

  值得注意的是, 这个属性是数组类型; 并且服务端不能假设上传的`数据内容`一定`真实`;

## 六、请求过滤

---

  当我们使用路由过滤回调注册作为中间件的时候, 我们可能会思考应该怎么使用它?

  下面我们介绍几个内置库提供的方法, 来帮助大家编写一些简单的过滤器代码:

### 1. http.ok

---

  当在`before`方法注册的回调函数返回`http.ok()`后, 允许后续继续调用用户注册的路由回调函数.

  你也可以直接写`return 200`来替代`http.ok()`, 他们的结果是一样的;

### 2. http.redirect(code, url)

---

  当在`before`方法注册的回调函数返回`http.redirect(301, 'https://cfadmin.cn/')`后, 则将请求重定向到指定链接上;

  注意: 

  * 参数`code`只能是`301`、`302`、`303`、`307`、`308`的其中一个;

  * 参数`url`则必须以`https/http`或`/`开头;

### 3. http.throw(code, html)

---

  当在`before`方法注册的回调函数返回`http.throw(404, "<title>错误</title>")`后, 将使用指定的`code`状态码来进行返回(仅允许`400-600`之间的错误码);

  第二个参数为可选的`html`代码, 作为自定义错误码的内容(可以在调试阶段将错误日志打印出来).

  如果不提供`html`参数, 将会根据是否配置了`error_pages`决定输出结果;


## 七、httpd包装器

  以下涉及到的方法都在httpd目录下, 请自行根据实际情况引用.

### 1. 异常包装器

  `function Throw(code, response) end`

  让注册的`USE`、`API`路由可以合法的抛出异常.

  * `code` - `HTTP`状态码(`400`-`600`).

  * `response` - 异常附带的响应体.


### 2. 跳转包装器

---

  `function redirect(code, url) end`

  让注册的`USE`、`API`路由可以合法的重定向.

  * `code` - `HTTP`状态码(`301`/`302`/`303`/`307`/`308`).

  * `url` - 合法的重定向的`URL`.


### 3. 响应包装器

---

  - [x] `function file_response(filename, filetype, fileinline) end`

  让注册的`USE`、`API`路由可以将文件包装为一个响应, 告知`httpd`并响应给客户端.

  * `filename`- `string`类型, 指定合法且完整的`文件路径`(如: `static/index.html`)

  * `filetype`- `string`类型, 指定合法且完整的`文件类型`(如: `text/css`)

  * `fileinline`- `boolean`类型, 指定响应的文件是否需要内嵌到浏览器.


  - [x] `function text_response(ctext, ctype) end`

  让注册的`USE`、`API`路由可以将`ctext`字符串包装为一个响应, 告知`httpd`并响应给客户端.

  * `ctext`- `string`类型, 合法且完整的`响应内容`(如: `<html>Hello World.</html>`)

  * `ctype`- `string`类型, 合法且完整的`响应类型`(如: `text/html`)


## 八、示例

---

  最后, 我们给出一个简单的示例以供大家自由扩展与调试.

```lua
local httpd = require "httpd"
local app = httpd:new("WebService")

local http = require "httpd.http"

-- 异常包装器
local http_throw = require "httpd.Throw"

-- 跳转包装器
local http_redirect = require "httpd.Redirect"

-- 响应包装器
local http_response = require "httpd.Response"
-- 文件类型
local file_response = http_response.file_response
-- 文本类型
local text_response = http_response.text_response

require "utils"

app:before(function (content)
  if true then
    return http.ok()
  end
  --[[
  if true then
    return http.redirect(301, 'https://cfadmin.cn/')
  end
  if true then
    return http.throw(431, '<h1> This is 413 Error, too long request header</h1>')
  end
  --]]
end)

app:api("/api", function (content)
  return http_throw(505, '{"code":505,"msg":"请求失败"}')
end)

app:api("/redirect", function (content)
  return http_redirect(302, "/api")
end)

--[[ 访问：/error/内部服务器错误 ]]
app:use("/error/{info}", function (content)
  return http_throw(500, "错误: " .. content.query.info)
end)

--[[ 访问：/jump/www.baidu.com 重定向到`https://www.baidu.com` ]]
app:use("/jump/{url}", function (content)
  return http_redirect(301, "https://" .. content.query.url)
end)

--[[ 访问：/view/static/index.html 会响应`static`文件夹下的`index.html` ]]
app:use("/view/static/{name}.html", function (content)
  return file_response("static/" .. content.query.name .. ".html", "text/html")
end)

--[[ 访问：/static/welcome.html, 将会改编content-type为application/json ]]
app:use("/test/{id}", function (content)
  return text_response('{"id":[1,2,3]}', "application/json")
end)

app:enable_rest()

app:listen("0.0.0.0", 8080)

app:run()
```

## 九、结束

---

  以上为`lua`的`httpd`使用内容.