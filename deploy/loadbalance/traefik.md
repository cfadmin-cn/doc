## 一、介绍

---

  `traefik`是一个云原生的新型的 HTTP 反向代理、负载均衡软件，能轻易的部署微服务.

  `traefik` 支持较多的后端协议(http[s]、Websocket、TCP), 可以对配置进行自动化、动态的管理.

  `traefik`支持多种容器框架(`docker compose`、`Swarm`、`k8s`、`Mesos`), 配置可以在很少改动下进行架构迁移. 

## 二、标准部署

---

  首先, 我们使用一份最简单的`docker-compose`文件启动`traefik`.

  但是需要记住: 我们需要将`docker.sock`挂在到`traefik`的内部, 这样才能让`traefik`访问`docker api`.

```yaml
version: "2"
services:
  WebProxy:
    image: traefik
    command: --api --docker # Enables the web UI and tells Traefik to listen to docker
    ports:
      - "80:80"     # The HTTP port
      - "8080:8080" # The Web UI (enabled by --api)
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock # So that Traefik can listen to the Docker events
    networks:
      - local
```

  然后, 我们单独启动`WebApp`容器, 并且在其下面的`labels`中定义相关行为; 这样在运行后会自动被`traefik`动态感知。

  1. `traefik.port`用于置顶当前容器需要代理的端口.

  2. `traefik.backen`为容器`service`名称.

  3. `domain`与`rule`则根据实际情况的域名进行指定. 本地调试可以使用`localhost`.

```yaml
version: "2"
services:
  WebApp:
    image: candymi/cfweb:latest
    labels:
      - "traefik.port=8080"
      - "traefik.backend=WebApp"
      - "traefik.enable=true"
      - "traefik.domain=localhost"
      - "traefik.frontend.rule=Host:localhost"
    volumes:
      - ./script:/app/script
    networks:
      - local
```
  
  `Traefik`与需要代理的容器之间必须网络互通. 否则会出现`gateway timeout`相关错误提示.

```yaml
version: "2"
services:
networks:
  local:
    driver: bridge
```

## 三、高级特性

---

### 1. 架构启动

---

  让我们进入的`docker`文件夹下. 这里已有了一份完整的`docker-compose-with-traefik.yaml`文件.

  然后我们使用`docker-compose -f docker-compose-with-traefik.yaml up -d`启动我们的应用程序.

```bash
[candy@MacBookPro:~/cfadmin/docker] $ docker-compose -f docker-compose-with-traefik.yaml up -d
Creating network "docker_local" with driver "bridge"
Creating docker_WebProxy_1 ... done
Creating docker_WebApp_1   ... done
[candy@MacBookPro:~/Documents/cfadmin/docker] $ ps
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                                        NAMES
8803daa412f7        candymi/cfweb:latest   "./cfadmin"              53 seconds ago      Up 51 seconds                                                    docker_WebApp_1
021af162cdb4        traefik                "/traefik --api --do…"   53 seconds ago      Up 51 seconds       0.0.0.0:80->80/tcp, 0.0.0.0:8080->8080/tcp   docker_WebProxy_1
[candy@MacBookPro:~/cfadmin/docker] $
```

  使用我们使用浏览器打开`localhost:/api/login`或者使用`curl -i localhost:/api/login`可以看到请求返回.

  这时候, 我们使用命令查看集群日志输出`docker-compose -f docker-compose-with-traefik.yaml logs`;

```bash
[candy@MacBookPro:~/cfadmin/docker] $ docker-compose -f docker-compose-with-traefik.yaml logs
Attaching to docker_WebApp_1, docker_WebProxy_1
WebApp_1    | [2019/04/26 10:33:59] - 172.31.0.2 - 172.31.0.1 - /api/login - GET - 200 - req_time: 0.000324/Sec
[candy@MacBookPro:~/cfadmin/docker] $
```

  可以看到, 集群已经正常启动了.

### 2. 架构扩容

---

  前面介绍过, `traefik`可以动态更新配置进行扩容(收缩). 那么我们应该这么做才能实现?

  我们知道`docker`有`scale`可以动态添加与收缩`container`数量. 我们前面已经启动过一个WebApp进程, 现在直接对他进行扩容即可.

  我们还是使用`docker-compose -f docker-compose-with-traefik.yaml`启动cf实例. (前面启动过后就不用执行这步命令)

  然后使用`docker-compose -f docker-compose-with-traefik.yaml --scale WebApp=3`命令将container扩展为3个.

```bash
[candy@MacBookPro:~/cfadmin/docker] $ docker-compose -f docker-compose-with-traefik.yaml --scale WebApp=3
Starting docker_WebApp_1 ... done
Creating docker_WebApp_2 ... done
Creating docker_WebApp_3 ... done
[candy@MacBookPro:~/cfadmin/docker] $
```

  再次使用`docker ps`命令来查看容器启动情况:

```bash
[candy@MacBookPro:~/cfadmin/docker] $ clear
[candy@MacBookPro:~/cfadmin/docker] $ docker ps
CONTAINER ID        IMAGE                  COMMAND                  CREATED              STATUS              PORTS                                        NAMES
cb53cb7c5892        candymi/cfweb:latest   "./cfadmin"              About a minute ago   Up About a minute                                                docker_WebApp_2
86ec0f97be0d        candymi/cfweb:latest   "./cfadmin"              About a minute ago   Up About a minute                                                docker_WebApp_3
8803daa412f7        candymi/cfweb:latest   "./cfadmin"              21 minutes ago       Up 2 minutes                                                     docker_WebApp_1
021af162cdb4        traefik                "/traefik --api --do…"   21 minutes ago       Up 2 minutes        0.0.0.0:80->80/tcp, 0.0.0.0:8080->8080/tcp   docker_WebProxy_1
[candy@MacBookPro:~/cfadmin/docker] $
```

  由此可以看出`WebApp`扩容已经完成. 现在可以在控制台看到`backend-WebApp`的Server数量已经有3个了.

## 三、测试

---

  `traefik`默认使用`rr算法`对后端服务进行负载均衡代理.

  `WebApp`容器对每个`http`请求都会有记录日志打印, `Docker`也会在`stdout`日志输出加入前缀. 这样我们就知道请求分发到哪个容器内部了.

  这里我们使用3次命令`curl localhost:api/login`来测试, 最后在`docker`内日志输出如下:

```bash
Attaching to docker_WebApp_2, docker_WebApp_3, docker_WebApp_1, docker_WebProxy_1
WebApp_3    | [2019/04/26 11:00:23] - 192.168.16.4 - 192.168.16.1 - /api/login - GET - 200 - req_time: 0.000534/Sec
WebApp_1    | [2019/04/26 11:00:24] - 192.168.16.4 - 192.168.16.1 - /api/login - GET - 200 - req_time: 0.000247/Sec
WebApp_2    | [2019/04/26 11:00:24] - 192.168.16.4 - 192.168.16.1 - /api/login - GET - 200 - req_time: 0.000297/Sec
```

  从日志中可以看到, 请求确实会被轮询到所有`WebApp`应用上.