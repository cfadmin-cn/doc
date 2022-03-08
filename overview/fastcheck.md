## 一、快速预览

---

  让我们运行作者制作的镜像, 来快速预览`cfadmin`.

### 1. 下载框架

---

  我们需要将`cfadmin`克隆到本地, 并且进入到框架的`docker`目录下. 如下所示:

```bash
[candy@MacBookPro:~] $ git clone https://github.com/CandyMi/cfadmin
 Cloning into 'cfadmin'...
 remote: Enumerating objects: 119, done.
 remote: Counting objects: 100% (119/119), done.
 remote: Compressing objects: 100% (91/91), done.
 remote: Total 7293 (delta 44), reused 65 (delta 25), pack-reused 7174
 Receiving objects: 100% (7293/7293), 3.07 MiB | 36.00 KiB/s, done.
 Resolving deltas: 100% (4607/4607), done.
[candy@MacBookPro:~] cd cfadmin/docker
[candy@MacBookPro:~/cfadmin/docker]
```


### 2. 安装工具

---

  按照下面的步骤安装`docker`与`comspose`部署工具:

  1. 使用包管理工具安装`docker-ce`, 如: `apt`、 `yum`、`pacman`等等.

  2. 点击[这里](https://github.com/docker/compose/releases)根据系统选择合适的`docker-compose`版本下载到本地.

### 3. 快速启动

---

  使用`docker-compose`命令一键启动`cfadmin`, 如果提示权限不足请在`命令`前面加上`sudo`.

  下面的命令将会将预览镜像拉取到本地, 然后会根据配置文件`部署`到`docker`中.

```bash
[candy@MacBookPro:~/cfadmin/docker] docker-compose -f docker-compose-with-cfadmin.yaml up
```

### 4. 测试使用

---

  现在我们可以尝试在浏览器中打开`http://localhost:8080/admin`就会显示登录页面. (账号/密码:`admin`)

  <img src="/images/pre-login.png"></img>

  <img src="/images/pre-dashboard.png"></img>

  <img src="/images/pre-profile.png"></img>

## 二、疑问解答

---

  Q1. 为什么启动期间cf会一直打印无法连接的日志呢?

  A1. `MySQL`镜像在第一次启动的时候会进行一些初始化工作, 这些工作是`MySQL`启动之前必须要做的工作. 而这些工作在完成之前需要不断尝试.有兴趣可以请移步[docker hub](https://hub.docker.com/)上的[MySQL](https://hub.docker.com/_/mysql)镜像地址寻找参考答案.

---

  Q2. 容器与宿主机的时间有时差怎么办?
  
  A2. 查看`docker-compose-with-cfadmin.yaml`文件, 文件注释有教您如何替换`localtime`来解决时区问题.

---

  Q3. 如果暂时停止, 待我下次有空启动再继续使用?

  A3. 可以使用`docker-compose -f docker-compose-with-cfadmin.yaml down`来停止运行中的容器. 需要再使用的时候请执行`docker-compose -f docker-compose-with-cfadmin.yaml start`命令来启动并且继续使用.

---

  Q4. 如何查看`cfadmin`的镜像? 在哪得到制作使用的`Dockerfile`?

  A4. 已经制作好的镜像在[这里](https://hub.docker.com/r/candymi/cfweb)查看, `Dockerfile`在[这里](https://github.com/CandyMi/cfadmin/blob/master/docker/Dockerfile)得到.

## 三、继续深入

---

  * 翻阅文档,学会使用.

  * 阅读源码,了解细节.