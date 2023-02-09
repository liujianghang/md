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

**2.events块**

**3.http块**
