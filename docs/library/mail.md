## 一、介绍

---

  `mail`库是一个基于`SMTP`实现的`简单邮件传输协议`(Simple Mail Transfer Protocol).

## 二、使用

---

  使用前请先导入`local mail = require "mail"`

## 三、参数

---

  函数原型: `mail.send(opt)`

  参数`opt`是一个配置表, 具体内容如下所示:

  * `opt.host` - `String`类型; 此参数是`smtp`邮件服务器`主机名`或`IP地址`.

  * `opt.port` - `Integer`类型; 此参数是`smtp`邮件服务器的`端口号`.

  * `opt.username` - `String`类型; 此参数是`smtp`邮件服务器`用户名`.

  * `opt.password` - `String`类型; 此参数是`smtp`邮件服务器`密码`.

  * `opt.SSL`, `Boolean`类型; 此参数告诉`mail`库是否使进行`SSL`连接.

  * `opt.from`, `String`类型; 此参数必须是一个合法的`smtp`服务器发件人`邮箱地址`.

  * `opt.to`, `String`类型; 此参数必须是一个合法的`smtp`收件人`邮箱地址`.

  * `opt.subject`, 此参数是`smtp`发送的邮件`主题`.

  * `opt.content`, 此参数是`smtp`此次发送的邮件`内容`.

  * `opt.mime`, 此参数是`smtp`此次发送的邮件`content`类型; (默认为`text`, 可选值为`html`)


## 四、测试

---

```lua
local mail = require "mail"

local ok, err = mail.send {
    host = 'smtp.qq.com', -- 收件服务器
    port = 465,     -- 收件服务器端口
    username = "869646063", -- 用户名
    -- password = "qovppnukdbcabcdg", -- 密码或客户端授权码
    from = '869646063@qq.com', -- 发件人地址
    to   = 'xwmrzg@163.com',   -- 收件人地址
    subject = "测试邮件主题",       -- 主题
    SSL = true,                   -- 该端口是否安全连接端口
    mime = 'html',               -- 不填写该字段则为纯文本, 客户端不会解析html语法
    content = "这是一封测试邮件!", -- 邮件内容
}

print(ok, err)
```

## 五、注意

---


  1. 目前的`MIME`的支持只有`html`与`text`;

  2. 目前不支持批量发送邮件;