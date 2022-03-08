
  我们对使用的操作系统平台推荐如下: 

  * `生产环境`推荐使用: `Linux` 与 `BSD`

  * `开发环境`推荐使用: `MAC OSX` 与 `Windows`

## 一、安装依赖

  - [x] 工具依赖: gcc git libtool make autoconf automake

  - [x] 编译依赖: openssl-devel zlib-devel

  - [x] 框架依赖: libev libeio lua5.3+


## 二、安装方式

###  Mac OSX

  * 下载并安装[brew](https://brew.sh/index_zh-cn), 之后修改源地址为[国内加速源](https://mirrors.ustc.edu.cn/help/brew.git.html);

  * 使用[brew](https://brew.sh/index_zh-cn)包管理器安装系统级编译工具链;
 
  * 从框架的地址上下载框架的源码, 然后执行`build.sh`脚本文件自动安装框架依赖库;

  * 使用`make build`/`make rebuild`命令来(重新)编译框架;
  
### GNU / Linux

  * 使用`apt`/`yum`/`pacman`/`zypper`等包管理器命令就能安装系统级编译工具链;
 
  * 从框架的地址上下载框架的源码, 然后执行`build.sh`脚本文件自动安装框架依赖库;

  * 使用`make build`/`make rebuild`命令来(重新)编译框架;

### OpenBSD / FreeBSD

  * 使用`pkg`或包管理器安装完全的系统级编译工具链;
 
  * 从框架的地址上下载框架的源码, 然后执行`build.sh`脚本文件自动安装框架依赖库;

  * 使用`make build`/`make rebuild`命令来(重新)编译框架;

### Windows

  * MSYS2 - 移植了`Pacman`的`MSYS`的独立修改版, 在Windows平台上模拟Linux运行环境的兼容技术;

  * WSL/WSL2 - 微软与Canonical公司开发的在Windows (10+)运行原生Linux二进制可执行文件（ELF格式）的兼容层;

  开发者需要自行安装好`MSYS2`与`WSL/WSL2`环境, 后续框架的编译流程与`Linux`上基本一致;
    
  <b>注意</b>: `MSYS2`可以打包依赖进行静态移植但性能较差, `WSL`可以生成执行效率更高的代码但可移植性较差.