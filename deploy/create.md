  当我们想基于框架构建大规模集群服务的时候, Docker也就是我们目前来说最的选择.

  事实上作者也接收到很多对部署服务的建议与反馈, 希望简单的描述下载大规模部署环境中如何用`cfadmin`

## 一、构建本地镜像

---
  
  将框架`clone`到本地后进入`docker`目录, 使用`docker build -t luaweb:v1 .`命令自动构建基于centos7的私有镜像.

  构建完成后使用`docker images`命令可以在本地镜像列表内看到`luaweb`版本镜像, 事实上我们也是如此构建`cfweb`容器的.

  我们从`docker/Dockfile`文件可以看出, 这个文件不仅仅是一个示例也是我们对基础框架的一个依赖说明.

## 二、运行本地镜像

---

  使用命令: `docker run -ti -p 8080:8080 luaweb:v1` 则可以运行镜像开始测试.

  此时您就可以使用`docker ps | grep cfweb`看到刚刚运行的`cfweb`容器.

## 三、进行测试

---

  在您执行了`curl -v localhost:8080/api`命令后能得到类似返回后. 则说明您构建到镜像构建完成.

```bash
[candy@MacBookPro:~/cfadmin] $ curl -v localhost:8080/api
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 8080 (#0)
> GET /api HTTP/1.1
> Host: localhost:8080
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Date: Sun, 24 Mar 2019 09:56:45 GMT
< Allow: GET, POST, HEAD, OPTIONS
< Access-Control-Allow-Origin: *
< Access-Control-Allow-Methods: GET, POST, HEAD, OPTIONS
< server: Candy Server/1.0
< Connection: close
< Transfer-Encoding: identity
< Content-Length: 51
< Content-Type: application/json
< Cache-Control: no-cache, no-store, must-revalidate
< Cache-Control: no-cache
<
* Closing connection 0
{"code":200,"message":"This is GET method request"}
[candy@MacBookPro:~/cfadmin]
```

## 四、使用docker-compose实现接入层(代理)服务(业务)分离

---

  当大家使用`Docker`在构建生产环境的时候, 一般会在开发框架前端使用代理软件(7层或4层)进行业务隔离与划分.

  实际开发框架的时候基本上都会建立一套约定的`http preix path`, 例如: `/api/login`, `/api/products`.

  有了这些(或更详细)约定的前缀, 可以很清晰的划分出接口的作用； 同时也方便项目的维护与后期的迭代.

  事实上作者当初在自己的业务中也考虑到这点, 以下是一种最基础的7层`Proxy`方案:

```bash
                 cfadmin.io/api -> cf httpd api
(cfadmin.io) nginx ->
                 cfadmin.io/ws  -> cf http websocket
```

  `docker-compose`编排文件使用`Nginx`作为前端接入层, 将容器`Link`到代理内部. 这样做的好处在于:

  1. 消除语言/框架之间的隔离, 方便使用者清晰的组合实际业务.

  2. 更加方便的管理自己的服务器, 也是Devops的最佳选择.

  3. 为构建高性能、可扩展的架构打下基础.

  注意: 以上具体配置文件与`docker-compose`编排文件都可在`docker`目录下找到.