## 一、目录介绍

---

  * `3rd`  开发者自定义第三方库目录

  * `logs` 框架日志文件夹

  * `luaclib` 框架内置的Lua C库目录

  * `lualib`    框架内置的Lua库的目录

  * `src`       框架核心源码

  * `script`    框架默认的`入口文件`目录

  * `cfadmin`   编译完成后会自动在当前文件夹的根目录生成的`cf框架启动文件`

  框架使用`cfadmin`可执行文件管理`main.lua`脚本. 默认的路径是`script/main.lua`, 但是可以使用不同参数来替换.

## 二、开始使用
  
  框架的最新版本可以直接使用`./cfadmin -h`命令来查看帮助:
  
```bash
$ ./cfadmin.exe -h
cfadmin System  : Windows(10.2.0)

cfadmin Version : 1.0

cfadmin Usage: ./cfadmin [options]

    -h <None>           "Print `cfadmin` usage."
    
    -d <None>           "Make `cfadmin` run in daemon mode."
    
    -e <FILENAME>       "Specified `lua` entry file name."
    
    -p <FILENAME>       "Specified the process `Pid` write file name."
    
    -k <Pid | File>     "Send `SIGKILL` signal to `Pid` or `Pid File`."
    
    -w <number Process> "Spawn specified number of worker processes."
```

  上述的大部分参数不仅可以帮助用户定制项目结构, 还可以作为进程管理器来使用. 不过这通常是高级用户才会用到.
  
## 三、多核利用

  默认情况下框架只会运行一个`Master`进程与一个`Worker`进程, 这样可以解决大部分开发者的一些基础开发工作.
  
  单进程不仅可以让开发者无需依赖外部缓存, 而且可以利用内置数据结构来简化开发. 然而这样会浪费硬件的资源;
  
  但是框架提供有以下几种选择来解决开发者的难题:
  
  * 使用作者提供的`容器技术`编排启动多实例利用多核心, 但这也会无形的影响到开发与测试阶段的效率;

  * 通过使用`-w`参数指定`Worker`进程数量(`1`-`255`), 但测试过只在`Linux kernel 3.9+`上能有效使用;


  第二种技术的原理是开启`端口复用`功能后将进程的事件唤醒交给内核来调度, 解决了之前的`惊群`的问题提高效率;

## 四、后台执行

  在本地的开发与测试期间, 开发者可以开启一个终端通过命令行让`cfadmin`在前台启动与停止.
  
  而当把应用程序部署在服务器上执行后, 可以通过加上`-d`参数让`cfadmin`在后台运行.