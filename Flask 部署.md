# Flask部署

### Gunicorn+Flask部署

部署过程均在Linux环境下运行

#### 安装gunicorn

**进入到虚拟环境**

``` shell
$ pip install gunicorn

$ pip show gunicorn
Name: gunicorn
Version: 19.7.1
Summary: WSGI HTTP Server for UNIX
Home-page: http://gunicorn.org
Author: Benoit Chesneau
Author-email: benoitc@e-engura.com
License: MIT
Location: /home/jian/.pyenv/versions/3.6.7/lib/python3.6/site-packages
Requires: 
Required-by:
```

**验证是否安装成功**

``` shell
$ gunicorn -h
# 但是会显示 -bash:gunicorn:command not found
```

因为安装完成后gunicorn可执行文件会存在于python的bin/文件夹下，如果是使用的系统Python环境，则通常会存在于/usr/local/python3/bin/gunicorn，如果是使用的Python的虚拟环境，则通常会存在于虚拟环境目录./venv/bin/gunicorn)。需要通过软链接将其链接到/usr/bin目录下，如下:

``` shell
# 系统python：
$ ln -s /usr/loacal/python/bin/gunicorn /usr/bin/gunicorn
# 虚拟环境目录
$ ln ./venv/bin/gunicorn /usr/bin/gunicorn
```

添加软连接后查看版本

``` shell
$ gunicorn -v
gunicorn (version 20.1.0)
```

#### 测试

**Flask应用测试**

启动

``` shell
$ python app.py
```

确认程序是否可以运行

``` shell
$ curl 127.0.0.1:5000/get
```

只要有返回就代表可以运行

然后执行 gunicorn app:app 第一个app是文件名，第二个是flask应用实例 app = Flask(\_\_name\__)
通过gunicorn启动Flask应用，默认会监听**127.0.0.1:8000**

``` shell
$ gunicorn app:app

# 以下是输出
1 [2022-03-27 15:53:53 +0800] [76948][INFO] Starting gunicorn 20.1.0
2 [2022-03-27 15:53:53 +0800] [76948][INFO] Listening at: http:/ /127.0.0.1:8000 (76948)
3 [2022-03-27 15:53:53 +0800] [76948][INFO] Using worker: sync
4 [2022-03-27 15:53:53 +0800] [76954][INFO] Booting worker with pid: 76954
5 [2022-03-27 15:53:55 +0800] [76948][INF0] Handling signal: int
6 [2022-03-27 15:53:55 +0800] [76954][INFO] worker exiting (pid: 76954)
7 [2022-03-27 15:53:55 +0800] [76948][INF0] Shutting down: Master
```

#### gunicorn配置

gunicorn可以配置一些基本配置，格式为

+ *gunicorn  -w 进程数量 -b 监听地址：监听端口 运行文件名称：Flask程序实例名 （0.0.0.0才可以监听所有的网卡，-D表示置于后台运行，退出shell也能运行*

所以改变运行的方式

``` shell
$ gunicorn -w 4 -b 0.0.0.0:8080 app:app -D 
```

查看所有有gunicorn字符的进程

``` shell
$ ps -ef | grep gunicorn 
```

杀死所有gunicorn进程

``` shell
$ ps -ef | grep gunicorn | awk '{print $2}' | xargs kill 
```

如果是生产环境，需要配置日志

``` shell
$ gunicorn -w 4 -b 0.0.0.0:8080 --access-logfile access.log --error-logfile error.log app:app -D
# 此后目录下面会多两个文件 access.log 和 error.log
```

查看日志

``` shell
# 获取末尾的几行日志
$ tail access.log
# 一直去监听这个文件
$ tail -f access.log
```

最后通过一个通用配置文件直接来启动程序

首先需要安装gevent（一种高性能的python并发框架）

``` shell
$ pip install gevent
```

``` python
# 创建一个gun.py文件
# gun.py
import multiprocessing
import gevent.monkey
gevent.monkey.patch_all()

bind = '0.0.0.0:8080'  # 绑定的ip以及端口号
chdir = '/home/flaskProject'  # gunicorn要切换到的目的工作目录
timeout = 60  # 超时
worker_class = 'gevent'  # 使用gevent模式，还可以使用sync 模式，默认的是sync模式
workers = multiprocessing.cpu_count() * 2 + 1  # 启动的进程数
loglevel = "info"  # 日志级别，这个日志级别指的是错误日志的级别，而访问日志的级别无法设置
access_log_format = '%(t)s %(p)s %(h)s "%(r)s" %(s)s %(L)s %(b)s %(f)s" "%(a)s"'  # 设置gunicorn访问日志格式，错误日志无法设置
pidfile = "gunicorn.pid"
accesslog = "access.log"
errorlog = "error.log"
daemon = True  # 是否后台运行
```

执行`gunicorn -c gun.py app:app`启动应用程序，

启动后项目的目录下会生成`access.log`，`error.log`和`gunicorn.pid`三个文件，`gunicorn.pid`中保存了gunicorn的主进程PID号，可以通过`cat gunicorn.pid`查看，当想要停止gunicorn时，直接`kill 进程号`即可杀死所有gunicorn进程。

#### 总结

单纯的Flask 自带的Web服务器做下测试，在压力大的时候出现socket的问题，因为他是单进程单线程的。而使用gunicorn来启动，响应速度和能力提升显著。配置中workers指定启动的进程数。cpu的损耗是平均到各个进程。workers的值一定不要过大，毕竟多进程对于系统的调度消耗比较大。

### nginx

`nginx`是一个功能强大的反向代理服务器，我们使用`nginx`来转发`gunicorn`服务。为什么要在`gunicorn`之上再加层`nginx`呢？一方面`nginx`可以补充`gunicorn`在某些方面的不足，如`SSL`支持、高并发处理、负载均衡处理等，另一方面如果是做一个`web`网站，除了服务之外，肯定会有一些静态文件需要托管，这方面也是`nginx`的强项

#### 安装与运行

```shell
$ sudo apt install nginx
```

编辑一些配置项

``` shell
$ sudo vi /etc/nginx/sites-available/default
```

``` python
# 注意下这里的监听端口，访问的时候会用到
listen 80 default_server;
listen [::]:80 default_server;

location / {
    # First attempt to serve request as file, then
    # as directory, then fall back to displaying a 404.
    try_files $uri $uri/ =404;
    proxy_pass http://localhost:8080/;
    proxy_redirect off;

    proxy_set_header Host $http_post;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

接下来重启`nginx`服务

``` shell
$ sudo /etc/init.d/nginx restart
```

因为是80端口 直接127.0.0.1就可以验证（前提一定是要把gunicorn打开）

### supervisor

`supervisor`是一个用`python`语言编写的进程管理工具，它可以很方便的监听、启动、停止、重启一个或多个进程。当一个进程意外被杀死，`supervisor`监听到进程死后，可以很方便的让进程自动恢复，不再需要程序员或系统管理员自己编写代码来控制，

``` shell
$ sudo apt install supervisor
$ cd /etc/supervisor/conf.d/
$ ls
```

编写第一个配置文件

``` shell
$ sudo vim gunicorn.conf
```

加入以下内容**（以下只是一个例子，实际中有很多需要修改的）**

``` c
[program:gunicorn]
command=/home/xugaoxiang/anaconda3/bin/gunicorn -w 2 -b :8080 run:app
directory=/home/xugaoxiang/workshop/Flask-10-wtf  # 这个是运行目录
autostart=true  # 以下是一些配置
autorestart=true
user=xugaoxiang # 这里是用户
redirect_stderr=true

```

查看nginx进程和gunicorn进程

``` shell
$ ps ax | grep nginx
$ ps ax | grep gunicorn
```

关掉上述的服务，查看是否还存在

``` shell
$ ps ax | grep gunicorn
```

最后重启superviosr服务

``` shell
$ sudo /etc/init.d/supervisor restart 
```

查看是否成功启动

``` shell
$ ps ax | grep gunicorn
$ ps ax | grep nginx
```

