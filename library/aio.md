## 一、基本介绍

---

  `aio`是基于`事件驱动`改造的文件I/O库, 可以在不阻塞进程的情况下实现多协程之间的调度. 因此提升了框架的整体执行效率.


## 二、使用方法


  下面我们一起来学习`aio`库的提供的文件操作方法.

### 1. 方法介绍

---

  函数原型:`aio.create(filename)`

  此方法将会创建一个`filename`文件;

  创建成功返回`__AIO__`对象, 否则返回`nil`与出错信息(文件已存在).

---

  函数原型:`aio.open(filename)`

  此方法将会打开(不存在则创建)一个`filename`文件;

  打开成功返回`__AIO__`对象(无论是否存在), 否则返回`nil`与出错信息(例如: 没有操作权限).

---

  函数原型:`aio.stat(path)`

  此方法将返回`path`参数所在文件/文件夹内部属性;

  返回值是一个`table`类型字典值, 内部包含了/文件夹的各种属性.

---

  函数原型:`aio.dir(path)`

  此方法将返回`path`参数所在目录下所有文件与文件夹名称;

  返回值是一个`table`类型数组值, 数组是文件与文件夹名称(字符串).

---

  函数原型:`aio.rmdir(dir)`

  此方法将删除`dir`参数目录;

  返回值是一个`boolean`类型值, 这个返回值表示删除是否成功.

---

  函数原型:`aio.mkdir(dir)`

  此方法将创建`dir`参数目录;

  返回值是一个`boolean`类型值, 这个返回值表示创建是否成功.

---

  函数原型:`aio.rename(old_name, new_name)`

  此方法会将`old_name`的文件名称改为`new_name`.

  返回值为`true`表示修改成功, 其他都表示修改失败.

---

  函数原型:`aio.currentdir(path)`

  此方法将获取`path`参数指定的`文件/文件夹`完整路径.

  返回值为`string`类型, 内容为当前`path`参数指定的`文件/文件夹`完整路径.

---

  函数原型:`aio.truncate(filename, offset)`

  此方法将`filename`参数指向的文件进行`扩容`与`缩容`.

  在磁盘容量与文件系统允许的情况下, 此方法永远会返回`true`;

  需要注意的是: "这个操作非常危险, 除非你明确知道你在做什么";  `offset`为0表示清空文件内容.

---

  函数原型:`aio.popen(command, timeout)`

  此方法将创建一个子进程运行`command`参数指定的`shell`脚本, 可选的`timeout`参数子进程运行多久后主动`kill`掉子进程.

  子进程异常退出或者超时返回`false`与出错信息, 成功则会返回一个`pfile`对象, 可以用它读取子进程在`stdout`的结果.

  需要注意的是: 运行`command`的子进程退出或超时此方法才会返回; `pfile`需要调用`close`手动关闭, 否则可能会造成`fd`泄漏的问题.

---

  函数原型:`aio.kill(pid)`

  此方法将会调用系统方法`kill`来发送`SIGKILL`信号给`pid`指定的进程, 此方法永远会返回`true`.


### 2. 内置对象

```lua
local pfile = class("pfile")

-- 读取数据
function pfile:read(bytes)
  if not self.fd then
    return false, "fd closed."
  end
  bytes = toint(bytes)
  if bytes and bytes > 0 then
    return tcp_read(self.fd, bytes)
  end
  bytes = 65535
  local buffers = {}
  while true do
    local buf = tcp_read(self.fd, bytes)
    if not buf then
      break
    end
    buffers[#buffers+1] = buf
  end
  return tconcat(buffers)
end


-- 关闭文件描述符
function pfile:close()
  tcp_close(self.fd)
end
```

```lua
local File = class("__AIO__")

function File:ctor(opt)
  self.fd = opt.fd
  self.path = opt.path
  self.stat = opt.stat
  self.status = "open"
end

-- 触发gc时检查是否回收self.fd
function File:__gc()
  if self.fd and self.status == "open" then
    return self:close()
  end
  return true
end

-- 重置标志位
function File:lseek(read_offset, write_offset)
  if read_offset then
    self:read_lseek(read_offset)
  end
  if write_offset then
    self:write_lseek(write_offset)
  end
end

-- 重置read_offset; 这个方法一般情况下不会用到, 除非你非常明白自己在做什么.
function File:read_lseek(read_offset)
  if toint(read_offset) and toint(read_offset) >= 0 then
    self.read_offset = read_offset
  end
end

-- 重置write_offset; 这个方法一般情况下不会用到, 除非你非常明白自己在做什么.
function File:write_lseek(write_offset)
  if toint(write_offset) and toint(write_offset) >= 0 then
    self.write_offset = write_offset
  end
end

-- 读取文件指定大小内容; 除非调用read_lseek重置位置或有新内容写入, 否则超出文件长度后将会返回空字符串.
function File:read( bytes )
  if self.status == "closed" then
    return nil, "File already Closed."
  end
  bytes = toint(bytes)
  if not bytes or bytes <= 0 then
    if bytes == 0 then
      return "", 0
    end
    return nil, "Invalid file read bytes."
  end
  self.__READ__ = assert(not self.__READ__, "[File:read/readall ERROR] : This method cannot be called concurrently in multiple coroutines.")
  local stat, err = aio.stat(self.path)
  if not stat then
    self.__READ__ = nil
    return nil, err
  end
  self.stat = stat
  -- 这一段的意思是: 当存在offset则取offset, 否则将offset置0; 无特殊情况不需要改动此地方
  self.read_offset = stat.size - (stat.size - (toint(self.read_offset) and toint(self.read_offset) > 0 and toint(self.read_offset) or 0))
  local data, err = aio._read(self.fd, bytes, self.read_offset)
  self.read_offset = self.read_offset + #data
  self.__READ__ = nil
  return data, err
end

-- 读取文件所有内容; 除非调用read_lseek重置位置或有新内容写入, 否则超出文件长度后将会返回空字符串.
function File:readall()
  if self.status == "closed" then
    return nil, "File already Closed."
  end
  self.__READ__ = assert(not self.__READ__, "[File:read/readall ERROR] : This method cannot be called concurrently in multiple coroutines.")
  local stat, err = aio.stat(self.path)
  if not stat then
    self.__READ__ = nil
    return nil, err
  end
  self.stat = stat
  -- 这一段的意思是: 当存在offset则取offset, 否则将offset置0; 无特殊情况不需要改动此地方
  self.read_offset = stat.size - (stat.size - (toint(self.read_offset) and toint(self.read_offset) > 0 and toint(self.read_offset) or 0))
  local data, err = aio._read(self.fd, toint(self.stat.size), self.read_offset)
  self.read_offset = self.read_offset + #data
  self.__READ__ = nil
  return data, err
end

-- 写入文件
function File:write( data )
  if self.status == "closed" then
    return nil, "File already Closed."
  end
  if type(data) ~= 'string' or data == "" then
    return nil, "Invalid file write data."
  end
  self.__WRITE__ = assert(not self.__WRITE__, "[File:write ERROR] : This method cannot be called concurrently in multiple coroutines.")
  -- 如果没有构建write_offset, 则需要通过stat构建; 否则永远只从尾部开始写入.
  if not self.write_offset then
    if not self.stat then
      self.stat = aio.stat(self.path)
    end
    self.write_offset = self.stat.size
  end
  local size, err = aio._write(self.fd, data, self.write_offset)
  if type(size) == 'number' then
    self.stat.size = self.stat.size + size
    self.write_offset = self.write_offset + size
  end
  self.__WRITE__ = nil
  return size, err
end

-- function

-- 刷新缓存
function File:flush()
  if self.status == "closed" then
    return nil, "File already Closed."
  end
  self.__FLUSH__ = assert(not self.__FLUSH__, "[File:flush ERROR] : This method cannot be called concurrently in multiple coroutines.")
  local ok, err = aio.flush(self.fd)
  self.__FLUSH__ = nil
  return ok, err
end

-- 清空文件
function File:clean()
  if self.status == "closed" then
    return nil, "File already Closed."
  end
  self.__CLEAN__ = assert(not self.__CLEAN__, "[File:clean ERROR] : This method cannot be called concurrently in multiple coroutines.")
  local ok, err = aio.truncate(self.path, 0)
  if not ok then
    self.__CLEAN__ = nil
    return nil, err
  end
  local stat, err = aio.stat(self.path)
  if type(stat) ~= 'table' then
    self.__CLEAN__ = nil
    return nil, err
  end
  if toint(self.read_offset) then
    self.read_offset = 0
  end
  if toint(self.write_offset) then
    self.write_offset = 0
  end
  self.stat = stat
  self.__CLEAN__ = nil
  return true
end
```

## 三、 使用示例

  参考[这里](https://github.com/CandyMi/cfadmin/blob/master/script/test_aio.lua)