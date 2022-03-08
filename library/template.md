## 一、 基本介绍

---

  `template`库是一个用纯`Lua`编写的(编译型)模板引擎.


## 二、 模板语法

---
  {% raw %}
  * `{{ lua expression }}` - `lua expression`是一段`Lua表达式`作用为输出的结果, 一些特殊符号会被转义;

  * `{* lua expression *}` - `lua expression`是一段`Lua表达式`作用为输出的结果, 一些特殊符号不会被转义;

  * `{% lua code %}` - 执行里面的一段lua代码, 如: `{% for i = x, y do %} ... {% end %}`;

  * `{(template)}` - 导入其它模板文件, 同时支持传参: {(file.html, { message = "Hello, World" })};

  * `{-raw-}` - 用于包裹一段模板内容, 它将被`{-raw-} ... {-raw-}`包裹的内容按原样输出(不做任何处理)。

  * `{# comments #}` - `comments`内容仅作为`注释`, 不会包含在输出字符串内. 这段语法的作用类似`Lua`内的`--`与`--[[]]`;
  {% endraw %}
## 三、 内置API

---

  导入方式: `local template = require "template"`

### 1. template.caching

---

  函数原型: `template.caching(boolean)`

  此函数用于启用或禁用模板缓存，如果未传递任何参数，则返回模板缓存的当前状态。

  默认已经`启用`了模板缓存，但您可能希望在`开发`或`内存不足`的情况下禁用它。

### 2. template.precompile

---

  函数原型: `template.precompile(view, path, strip, plain)`

  将模板预编译为二进制块, 该二进制块可以作为文件写出（您可以将其直接与Lua的`load`和`loadfile`一起使用）.

  `cache_key`参数决定是否需要缓存模板; `strip`参数可以使预编译的模板也具有调试信息（默认为`true`）。

  `plain`参数决定将`view`当成模板解析还是当做模板所在文件路径:

  * 如果`plain`参数为`true`的时候, 模板引擎会将其认为是`纯字符串模板`而不会假设它可能会是一个模板路径;

  * 如果`plain`参数为`false`的时候, 模板引擎会将其认为是`模板文件路径`而不会假设它可能会是一个字符串模板;

  * 如果`plain`设置为`nil`（默认值），则模板不会将文件读取错误抛出（而是返回视图）。

### 3. template.compile

---

  函数原型: `template.compile(view, cache_key, plain)`

  解析、编译和缓存（如果启用了缓存）模板，并将编译后的模板作为将上下文作为参数并将渲染的模板作为字符串返回的函数返回。

  `cache_key`参数决定是否需要缓存模板; `plain`参数决定将`view`当成模板解析还是当做模板所在文件路径:

  * 如果`plain`参数为`true`的时候, 模板引擎会将其认为是`纯字符串模板`而不会假设它可能会是一个模板路径;

  * 如果`plain`参数为`false`的时候, 模板引擎会将其认为是`模板文件路径`而不会假设它可能会是一个字符串模板;

  * 如果`plain`设置为`nil`（默认值），则模板不会将文件读取错误抛出（而是返回视图）。

  注意第二个返回值是布尔值. 您可以丢弃它或使用它来确定返回的函数是否已缓存。

### 4. template.render

---

  函数原型: `template.render(view, context, cache_key, plain)`

  对参数`view`的模板内容进行编译、解析后输出; `Table`类型的参数`context`为`view`内可被引用的内容, 它被作为全局对象注入;

  `cache_key`参数决定是否需要缓存模板; `plain`参数决定将`view`当成模板解析还是当做模板所在文件路径:

  * 如果`plain`参数为`true`的时候, 模板引擎会将其认为是`纯字符串模板`而不会假设它可能会是一个模板路径;

  * 如果`plain`参数为`false`的时候, 模板引擎会将其认为是`模板文件路径`而不会假设它可能会是一个字符串模板;

  * 其它任何情况都将不会做任何操作.

## 四、 开始测试

---
  {% raw %}
```lua
local template = require "template"

print(template.render([[<title>{*title*}<title>]], { title = "Hello world" }, nil, true))
```
  {% endraw %}
```bash
[candy@MacBookPro:~/cfadmin] $ ./cfadmin
<title>Hello world<title>
```

## 五、更多示例

---
  {% raw %}
### 1. 模板渲染测试一

---

```lua
-- script/main.lua
local template = require "template" 
template.render("view.html", { message = "Hello, World!" })
```

```html
<!-- view.html -->
<!DOCTYPE html>
<html>
<body>
  <h1>{{message}}</h1>
</body>
</html>
```

### 2. 模板渲染测试二

---

```lua
local template = require "template"
template.render("view.html", {
  title   = "Testing lua-template",
  message = "Hello, World!",
  names   = { "James", "Jack", "Anne" },
})
```

```html
<!-- view.html -->
{(header.html)}
<h1>{{message}}</h1>
<ul>
{% for _, name in ipairs(names) do %}
    <li>{{name}}</li>
{% end %}
</ul>
{(footer.html)}
```

```html
<!-- header.html -->
<!DOCTYPE html>
<html>
<head>
  <title>{{title}}</title>
</head>
<body>
```

```html
<!-- foot.html -->
</body>
</html>
```

### 3. 模板渲染测试三

---

```lua
local view = [[
<h1>{{title}}</h1>
<ul>
{% for _, v in ipairs(list) do %}
    <li>{{v}}</li>
{% end %}
</ul>]]

local compiled = template.precompile(view)

local file = io.open("precompiled-bin.html", "wb")
file:write(compiled)
file:close()

-- 或者你也可以写（和上面一样）
template.precompile(view, "precompiled-bin.html")

template.render("precompiled-bin.html", {
    title = "Names",
    list = {"Emma", "James", "Nicholas", "Mary"}
})
```
{% endraw %}