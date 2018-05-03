## nginx+tomcat负载均衡配置

Original *2017-07-19* *李康* [码神联盟](https://mp.weixin.qq.com/s?__biz=MzIxMjg4NDU1NA==&mid=2247483847&idx=1&sn=4d3c6d6c331342cc930cb4cfc0ca465c&chksm=97be0ce2a0c985f427f0462a9be9dd49b963a1e09ea86ef68f081afb8a21f96f247708fbc52d&scene=21##)

![img](0-1525220480098.gif)

​        前几天，我们讲过了【[互联网常见架构模式 之 nginx负载均衡](http://mp.weixin.qq.com/s?__biz=MzIxMjg4NDU1NA==&mid=2247483785&idx=1&sn=66b01f7b73a5a3ea60133585a6a69d6d&chksm=97be0caca0c985ba9f080117c78874edc97f8d653c597be2e844c934d834b2f1bcff927b4ea7&scene=21#wechat_redirect)】(忘记的童鞋，点击上面链接或者输入关键词：nginx\负载均衡，即可重新阅读)，重点是概念和理论，以及负载的原理，今天我们来讲下，具体如何去配置负载，web服务器以Tomcat为例。

​        负载均衡的目的是为了解决单个节点压力过大，造成Web服务响应过慢，严重的情况下导致服务瘫痪，无法正常提供服务。

​        我们通常称对某一台机器的访问量称为负载量，如何将一个用户的请求，合理的分配到一台能快速响应用户请求的服务器上，我们就需要用到一些负载策略。也就体现出了文章主题的用意了：负载均衡，将用户的所有HTTP请求均衡的分配到每一台机器上，充分发挥所有机器的性能，提高服务的质量和用户体验。负载均衡可以通过负载均衡网络硬件设备和Web服务器软件来实现，前者设备成本较高，小公司通常负担不起，所以后者一般是我们的首选。实现负载均衡常用的Web服务器软件有Nginx、HAProxy、LVS、Apache，本文主要介绍Nginx的负载均衡策略。

![img](640-1525220480148)

​    下载nginx、tomcat和安装就不过多介绍了，某度有很多。

**准备：**

- ​    nginx-1.13.0
- ​    tomcat-8081
- ​    tomcat-8082
- ​    jdk7

**第一步：Tomcat-8081配置修改**

 修改tomcat的文件：server.xml（..\apache-tomcat-8081\conf）

将端口修改三处为：

1：<Server port="8005"...>

2：<Connector port="8081" .../>

3： <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />

**第二步：Tomcat-8082配置修改**

 修改tomcat的文件：server.xml（..\apache-tomcat-8082\conf）

将端口修改三处为：

1：<Server port="8006"...>

2：<Connector port="8082" .../>

3： <Connector port="8010" protocol="AJP/1.3" redirectPort="8443" />

![img](640-1525220480152)

**第三步：nginx配置修改**

修改nginx的配置文件：nginx.conf (...\nginx-1.13.0\conf)

修改后为（红色字体为修改内容，其它默认即可）：

```java
#user  nobody;

worker_processes  1;

#error_log  logs/error.log;

#error_log  logs/error.log  notice;

#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {

    worker_connections  1024;

}

http {

    include       mime.types;

    default_type  application/octet-stream;

    log_format  main  'remote_addr - remote_user [time_local] "request" '

                      'status body_bytes_sent "$http_referer" '

                      '"http_user_agent" "http_x_forwarded_for"';

    access_log  logs/access.log  main;

    sendfile        on;

    #tcp_nopush     on;

    #keepalive_timeout  0;

    keepalive_timeout  65;

    #gzip  on;

    upstream localhost {

	server 127.0.0.1:8081;

	server 127.0.0.1:8082; 

     }

    server {

        listen       80;

        server_name  localhost;

        #charset koi8-r;

        access_log  logs/host.access.log  main;

        location / {

	     proxy_pass  http://localhost;

        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html

        #

        error_page   500 502 503 504  /50x.html;

        location = /50x.html {

            root   html;

        }

    }

}

```



**第四步：将web项目部署到tomcat-8081和tomcat-8082中**

**第五步：启动tomcat-8081和tomcat-8082**

**第五步：启动nginx**

**最后：访问http://localhost**