
## 一、安装 Visual Studio Code

  * 打开[Visual Studio Code 官网](https://code.visualstudio.com/)首页, 点击里面的**下载**按钮开始下载.

  * 打开下载的安装文件, 根据提示进行安装. 安装完成之后, 打开`Visual Studio Code`.

## 二、安装插件

  打开下面的网页下载页面, 在打开的页面内点击`Install`按钮安装插件:

  - [x] [Code-Runner](https://marketplace.visualstudio.com/items?itemName=TabNine.tabnine-vscode)

  - [x] [Lua-language-server](https://marketplace.visualstudio.com/items?itemName=sumneko.lua)

  支持的环境

  - [x] Windows(WSL)

  - [x] Linux

  - [x] MAC OSX

## 构建与清理

  `构建`期间依赖不完整会报错. 所以在构建失败的后, 请先`清理`并且安装完框架依赖库后再次尝试`构建`.

### 1. 构建

  找到项目文件夹内的`build.sh`文件, 单击右键选择`Run Code`后点击执行即可.

### 2. 清理

  找到项目文件夹内的`clean.sh`文件, 单击右键选择`Run Code`后点击执行即可.

## 编译安装

  构建依赖完成后, 我们可以在终端输入`make build`或者`make rebuild`开始编译框架.

```bash
CandyMi@MacBookPro: ~/cfadmin$ make rebuild
********** Clean All Files **********
rm -rf cfadmin libcore luaclib/*.so 3rd/*.so
Done.
********** Core library and executable **********
CC      -       libcore
CC      -       cfadmin
********** Built-in core modules **********
CC      -       lsys
CC      -       lz
CC      -       ludp
CC      -       lfs
CC      -       ltask
CC      -       lffi
CC      -       ltimer
CC      -       laio
CC      -       ltcp
CC      -       lpbc
CC      -       lpeg
CC      -       lcrypt
CC      -       lcjson
CC      -       lmsgpack
CC      -       lhttpparser
Done.
```

  由于框架采用**并行编译**模式, 所以上述的输出结果可能是无序. 
  
  如果期间没有任何输出错误提示则表示编译成功, 并且可以看到当前目录已经生成了`libcore`动态库与`cfadmin`可执行文件.

## 配置示例

  上述所有内容完成后, 在项目根目录创建`.vscode`文件夹. 然后再文件夹内创建`settings.json`文件并将下面的内容复制进去并保存.

```json
{
  "Lua.diagnostics.globals": [
    "null", "NULL", "worker"
  ],

  "code-runner.runInTerminal": true,

  "code-runner.clearPreviousOutput": true,
  
  "code-runner.saveAllFilesBeforeRun": true,

  "code-runner.executorMap": {
    "lua": "./cfadmin -e"
  }

}
```

  * `Lua.diagnostics.globals`, 注册全局变量.

  * `runInTerminal`, 强制在终端运行. (无法由`Visual Studio Code`终止)

  * `clearPreviousOutput`, 每次运行都会清理之前的输出终端内容.

  * `saveAllFilesBeforeRun`, 每次运行都会保存所有文件.

## 运行程序

  如果根据上述步骤配置完毕, 那么我们现在可以开始尝试运行一些测试代码!
 
  在左侧项目目录下找到`script`目录并点击展开, 找到`test_protobuf.lua`后右键点击`Run Code`运行即可.
 
  运行之后, 终端就会输出类似如下内容:
 
```bash
CandyMi@MacBookPro:~/cfadmin$ ./cfadmin -e "/home/CandyMi/cfadmin/script/test_protobuf.lua"
[2021-07-21 18:04:24,521] [@/home/CandyMi/cfadmin/script/test_protobuf.lua:19] [DEBUG] : true, 243 
[2021-07-21 18:04:24,521] [@/home/CandyMi/cfadmin/script/test_protobuf.lua:33] [DEBUG] : "22 10 12 06 E5 8F B3 E8 84 9A 0A 06 E5 B7 A6 E8 84 9A 1A 10 12 06 E5 8F B3 E6 89 8B 0A 06 E5 B7 A6 E6 89 8B 10 FF FF FF FF 0F 0A 07 43 61 6E 64 79 4D 69" 
[2021-07-21 18:04:24,521] [@/home/CandyMi/cfadmin/script/test_protobuf.lua:35] [DEBUG] : {["hand"]={["right"]="右手", ["left"]="左手"}, ["foot"]={["right"]="右脚", ["left"]="左脚"}, ["age"]=4294967295, ["name"]="CandyMi"} 
[2021-07-21 18:04:24,521] [@/home/CandyMi/cfadmin/script/test_protobuf.lua:37] [DEBUG] : nil
```

  如果看到以上内容说明环境配置成功! 否则, 请继续根据前面步骤进行检查.
 
## 注意
 
  如果需要完全交由`Visual Studio Code`控制`cfadmin`的启动与停止, 可以注释`"code-runner.runInTerminal": true`配置.
 
  这样可以图形化控制启动与停止, 无需用户在命令行进行干预. 但是这种模式下输出结果可能会出现乱码问题, 请开发者自行酌情选择.

## 报错

  1. `./cfadmin: error while loading shared libraries: *.so: cannot open shared object file: No such file or directory`

  此错误提示表明框架依赖的动态库不存在, 请尝试重新构建依赖库与编译框架.
  
  2. `warning: ignoring return value of '******', declared with attribute warn_unused_result ... `

  此错误提示表明依赖库的语法检查比较严格, 开发者在构建期间一般无需理会.
  