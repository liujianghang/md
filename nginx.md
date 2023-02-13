# nginx

### 1.nginx的三大特性

* 反向代理

* 负载均衡

* 动静分离

### 2.安装nginx

**1.首先得联网，暂时关闭防火墙（可选）**

```shell
# 查看防火墙开启的端口
firewall-cmd --list-all
# 防火墙添加8080端口
sudo firewall-cmd --add-port=8080/tcp --permanent
# 重启防火墙
firewall-cmd-reload
# 停止防火墙
systemctl stop firewalld
# 永久关闭防火墙
systemctl disable firewalld
# 查看防火墙的状态
systemctl status firewalld
```

**2.安装nginx**

```shell
# 添加源(如果是centos系统)
sudo yum install epel-release
# 更新一下
yum update
# 下载
# 使用 yum 进行 Nginx 安装时，Nginx 配置文件在 /etc/nginx 目录下
yum install -y nginx
# 查看nginx状态
systemctl status nginx
# 开机启动
systemctl enable nginx
# 查看nginx进程
ps -ef | grep nginx
```

*nginx通过yum安装后的目录：*

* */usr/sbin/nginx*
* */usr/lib64/nginx*
* */etc/nginx* 
* */usr/share/nginx*
* /var/log/nginx/error.log(nginx的错误日志)

**3.测试**

### 3.nginx常用命令

```shell
# 查看nginx版本
nginx -v
# 停止nginx
nginx -s stop
# 启动nginx
nginx
# 重新加载nginx(配置文件被改写后)
nginx -s reload
```

### 4.nginx配置文件

```shell
cd /etc/nginx
ls
```

![image-20230209180153686](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209180153686.png)

*nginx的配置文件：nginx.conf由三个部分组成*

**1.全局块**

*从配置文件到events块之间的内容，主要会设置一些影响nginx服务器整体运行的配置指令*

```shell
#auto nginx处理并发的数量，这个值越大，能够支持的并发器也越多
worker_process 1;  
```

**2.events块**

*主要影响nginx服务器于用于的网络的连接*

````shell
# nginx 支持的最大连接数
worker_connections 1024;
````

**3.http块**

*代理，缓存和日志定义等绝大多数功能和第三方模块的配置都在这里，包含http块和server块*

* http全局块

*http全局块配置的指令包含文件引入，MIME-TYPE定义，日志自定义，连接超时时间，单链接请求数上限*

* server块

*这块和虚拟主机有密切联系，虚拟主机从用户角度出发，和一台独立的硬件主机是一样的，该技术的产生是为了节省互联网服务器硬件成本*

*每个http块可以包括多个server块，而每个server块就相当于一个虚拟主机*

*而每个server块也分为全局server块，以及可以同时包含多个location块。*

**全局server块**：最常见的配置是虚拟机主机的监听设置和本虚拟主机的名称或IP配置

**location块**：一个server块可以配置多个location块

### 5.nginx反向代理

**1.在windows系统的host文件进行域名和ip对应关系的匹配**

```
C:\Windows\System32\drivers\etc\hosts
```

```shell
# 修改相应的测试配置项
192.168.206.128  www.flasktest.com
```

**2.在nginx进行请求转发的配置（反向代理配置）**

```nginx
# 监听本地的80端口 server_name是本机的主机ip 转发到5000端口
server {
    listen       80;
    server_name  192.168.206.128;
    location / {
        proxy_pass http://127.0.0.1:5000;
    }
}
```

**3.根据不同的请求路径访问不同的服务器**

准备两个已经服务器的flask端口（5000，5001）和不同的路由路径。

```nginx
# 监听虚拟机的9001端口，然后转发到别的端口去）
server {
    listen       9001;
    server_name  192.168.206.128;
    location ~ /test1 {
        proxy_pass http://127.0.0.1:5000;
    }
    location ~ /test2 {
        proxy_pass http://127.0.0.1:5001;
    }
}
```

```shell
# 重新启动nginx
nginx -s stop
nginx
```

主机上测试访问：[www.flasktest.com:9001/test1](http://www.flasktest.com:9001/test1)

*location参数介绍：*

（1） `“=”` ，精确匹配：**内容要同表达式完全一致才匹配成功**

```
location = /abc/ {
  .....
 }
        
# 只匹配http://abc.com/abc
#http://abc.com/abc [匹配成功]
#http://abc.com/abc/index [匹配失败]
```

（2） `“~”`，执行正则匹配，区分大小写。

```
location ~ /Abc/ {
  .....
}
#http://abc.com/Abc/ [匹配成功]
#http://abc.com/abc/ [匹配失败]
```

（3）`“~*”`，执行正则匹配，忽略大小写

```
location ~* /Abc/ {
  .....
}
# 则会忽略 uri 部分的大小写
#http://abc.com/Abc/ [匹配成功]
#http://abc.com/abc/ [匹配成功]
```

**4.负载均衡**

```nginx
upstream my_flask {
    server 192.168.206.128:5000;  #flask 1
    server 192.168.206.128:5001;  #flask 2
}
server {
    listen       80;
    server_name  192.168.206.128;
    location / {
        proxy_pass http://my_flask;
    }
}
```

*nginx分配服务器的策略：*

（1）轮询模式（默认）
每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。

适合服务器配置相当，无状态且短平快的服务使用。也适用于图片服务器集群和纯静态页面服务器集群。

（2）weight权重模式
这种方式比较灵活，当后端服务器性能存在差异的时候，通过配置权重，可以让服务器的性能得到充分发挥，有效利用资源。weight和访问比率成正比，用于后端服务器性能不均的情况。权重越高，在被访问的概率越大

```nginx
upstream my_flask {
    server 192.168.206.128:5000 weight=5;  #flask 1
    server 192.168.206.128:5001 weight=10;  #flask 2
}
```

（3）ip_hash
上述weight权重模式方式存在一个问题，在负载均衡系统中，假如用户在某台服务器上登录了，那么该用户第二次请求的时候，因为我们是负载均衡系统，每次请求都会重新定位到服务器集群中的某一个，那么已经登录某一个服务器的用户再重新定位到另一个服务器，其登录信息将会丢失，这样显然是不妥的。
可以采用ip_hash指令解决这个问题，如果客户已经访问了某个服务器，当用户再次访问时，会将该请求通过哈希算法，自动定位到该服务器。
每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session不能跨服务器的问题。

```nginx
upstream my_flask {
	ip_hash;
    server 192.168.206.128:5000;  #flask 1
    server 192.168.206.128:5001;  #flask 2
}
```

（4）fair方式

按照后端服务器的响应时间来分配请求，响应时间短的优先分配。

```nginx
upstream my_flask {
    server 192.168.206.128:5000;  #flask 1
    server 192.168.206.128:5001;  #flask 2
    fair;
}
```

**5.动静分离**

nginx 的动静分离，指的是由 nginx 将客户端请求进行分类转发，静态资源请求（如html、css、图片等）由静态资源服务器处理，动态资源请求（如 jsp页面、servlet程序等）由 tomcat 服务器处理，tomcat 本身是用来处理动态资源的，同时 tomcat 也能处理静态资源，但是 tomcat 本身处理静态资源的效率并不高，而且还会带来额外的资源开销。利用 nginx 实现动静分离的架构，能够让 tomcat 专注于处理动态资源，静态资源统一由静态资源服务器处理，从而提升整个服务系统的性能 。

```shell
# 首先要创建合适的目录结构
```



![image-20230213144841803](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230213144841803.png)![image-20230213144919960](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230213144919960.png)

```nginx
# 更改nginx(注意这里要加上权限！！)
user root;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

...
 server {
        listen       80;
        server_name  192.168.206.128;
        
        location / {
            proxy_pass http://127.0.0.1:5000;
        }

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
        
        location /www {
            root /root/flask_login/static/;
            index test.html;
        }
        
        location /image {
            root /root/flask_login/static/;
        }
}
```

主机上测试访问：[test.png (2000×2000) (flasktest.com)](http://www.flasktest.com/image/test.png)

**6.一个完整的nginx配置案例**

```nginx
########### 每个指令必须有分号结束。#################
#user administrator administrators;  #配置用户或者组，默认为nobody nobody。
#worker_processes 2;  #允许生成的进程数，默认为1
#pid /nginx/pid/nginx.pid;   #指定nginx进程运行文件存放地址
error_log log/error.log debug;  #制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
events {
    accept_mutex on;   #设置网路连接序列化，防止惊群现象发生，默认为on
    multi_accept on;  #设置一个进程是否同时接受多个网络连接，默认为off
    #use epoll;      #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
    worker_connections  1024;    #最大连接数，默认为512
}
http {
    include       mime.types;   #文件扩展名与文件类型映射表
    default_type  application/octet-stream; #默认文件类型，默认为text/plain
    #access_log off; #取消服务日志    
    log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; #自定义格式
    access_log log/access.log myFormat;  #combined为日志格式的默认值
    sendfile on;   #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
    sendfile_max_chunk 100k;  #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    keepalive_timeout 65;  #连接超时时间，默认为75s，可以在http，server，location块。

    upstream mysvr {   
      server 127.0.0.1:7878;
      server 192.168.10.121:3333 backup;  #热备
    }
    error_page 404 https://www.baidu.com; #错误页
    server {
        keepalive_requests 120; #单连接请求上限次数。
        listen       4545;   #监听端口
        server_name  127.0.0.1;   #监听地址       
        location  ~*^.+$ {       #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
           #root path;  #根目录
           #index vv.txt;  #设置默认页
           proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表
           deny 127.0.0.1;  #拒绝的ip
           allow 172.18.5.54; #允许的ip           
        } 
    }
} 
```

