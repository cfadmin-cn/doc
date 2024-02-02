## 一、介绍

  `Nginx` (engine x) 是一个高性能的HTTP和反向代理`web`服务器.

  `Nginx`不仅是一款Web 服务器/反向代理服务器, 也是电子邮件代理服务器.

  `Nginx`将源代码以BSD许可证的形式发布，因它的稳定性、丰富的功能、示例配置文件、低系统资源消耗而闻名。

## 二、部署

---

  我们需要一份较为精简的配置文件来顺利启动`Nginx`, 使用`Nginx`的`upstream`创建一个名为负载均衡配置.

  然后将所有后端`service`地址与端口写入进去, 并将其命名为`nginx.conf`并保存到当前文件夹下.

  还需要在配置文件中指定`WebApp`容器的`name`为`host`, 同时将其`Link`到`WebProxy`容器中来. 这样`Nginx`才能顺利访问到它.

  具体示例如下所示:

```nginx
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
  worker_connections  65535;
}

http {
  include       /etc/nginx/mime.types;
  default_type  application/octet-stream;

  log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "req_time:[$request_time/Sec]" "upstream_time:[$upstream_response_time/Sec]" ';

  access_log  /var/log/nginx/access.log  main;

  #优化文件发送
  sendfile       on;
  #tcp_nopush     on;
  #关闭Nagle算法
  tcp_nodelay    on;

  keepalive_timeout  65;

  gzip  on;
  gzip_vary on;
  gzip_min_length 1k;
  gzip_comp_level 9;
  gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
  gzip_disable "MSIE [1-6]\.";

  upstream myweb {
    server webapp1:8080;
    server webapp2:8080;
    server webapp3:8080;
  }

  server {
    listen  80;
    #access_log  /var/log/nginx/8080.log  main;

    location /ws {
      proxy_pass http://myweb/ws;
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
    }

    location / {
      proxy_pass http://myweb;
      proxy_http_version 1.1;
      proxy_ignore_client_abort on;
      proxy_set_header Host $http_host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
  }
}
```

  配置文件准备好后, 我们开始编写`docker-compose`的编排文件.

```yaml
# docker-compose.yaml
version: "2"
services:
  WebProxy:
    image: nginx:latest
    ports:
      - 80:80
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    networks:
      - local
    links:
      - WebApp1:webapp1
      - WebApp2:webapp2
      - WebApp3:webapp3
  WebApp1:
    image: candymi/cfweb:latest
    volumes:
      - ./script:/app/script
    networks:
      - local
  WebApp2:
    image: candymi/cfweb:latest
    volumes:
      - ./script:/app/script
    networks:
      - local
  WebApp3:
    image: candymi/cfweb:latest
    volumes:
      - ./script:/app/script
    networks:
      - local

networks:
  local:
    driver: bridge
```

  我们将`nginx.conf`文件直接替换`Nginx`镜像的默认配置文件, 这样在启动的时候`nginx`将会使用我们编写的配置文件进行启动.

  然后我们在`links`下将`WebApp`链接到`Nginx`容器内, 这样就实现了`Nginx`与`WebApp`的`host`互通.

  现在我们使用这份配置文件进行启动:

```bash
[candy@MacBookPro:~/cfadmin/docker] $ docker-compose -f docker-compose-with-nginx.yaml up
Starting docker_WebApp2_1 ... done
Starting docker_WebApp1_1 ... done
Starting docker_WebApp3_1 ... done
Starting docker_WebProxy_1 ... done
Attaching to docker_WebApp2_1, docker_WebApp1_1, docker_WebApp3_1, docker_WebProxy_1
```

  上述配置, 我们创建了一个3个`WebApp`容器, 使用`upstream`进行反向代理将不同的请求分发到不同的`WebApp`容器内部.

## 三、测试

  如果启动没有任何问题后, 我们可以尝试使用curl进行测试.

```bash
WebApp1_1   | [2019/04/26 11:46:06] - 192.168.32.1 - 192.168.32.1 - /api/login - GET - 200 - req_time: 0.000249/Sec
WebProxy_1  | 192.168.32.1 - - [26/Apr/2019:11:46:06 +0000] "GET /api/login HTTP/1.1" 200 46 "-" "curl/7.54.0" "req_time:[0.001/Sec]" "upstream_time:[0.000/Sec]"
WebApp2_1   | [2019/04/26 11:46:07] - 192.168.32.1 - 192.168.32.1 - /api/login - GET - 200 - req_time: 0.000230/Sec
WebProxy_1  | 192.168.32.1 - - [26/Apr/2019:11:46:07 +0000] "GET /api/login HTTP/1.1" 200 46 "-" "curl/7.54.0" "req_time:[0.001/Sec]" "upstream_time:[0.000/Sec]"
WebProxy_1  | 192.168.32.1 - - [26/Apr/2019:11:46:08 +0000] "GET /api/login HTTP/1.1" 200 46 "-" "curl/7.54.0" "req_time:[0.002/Sec]" "upstream_time:[0.000/Sec]"
WebApp3_1   | [2019/04/26 11:46:08] - 192.168.32.1 - 192.168.32.1 - /api/login - GET - 200 - req_time: 0.000318/Sec
```

  可以看到`WebProxy`与`WebApp`容器都打印出了请求日志, 说明我们的容器已经开始正确运行.