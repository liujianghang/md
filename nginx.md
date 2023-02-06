# nginx

### 1.nginx的三大特性

* 反向代理

* 负载均衡

* 动静分离

### 2.安装nginx

**1.环境准备**

* 首先得联网

+ 暂时关闭防火墙（可选）

```
systemctl stop firewalld
```

永久关闭防火墙

```
systemctl disable firewalld
```

查看防火墙状态

```
systemctl status firewalld
```

* 关闭selinux（一种美国安全局对于强制访问控制的实现）

查看是否启用

```
sestatus
```

关闭

```
vim /etc/selinux/config
```

修改成如下

![image-20230206165130520](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230206165130520.png)



重新启动

```
shutdown -r now
```

**2.安装nginx**

* 安装所有依赖包

```
yum install -y gcc pcre pcre-devel zlib zlib-devel openssl openssl-devel
```

查看是否安装成功

```
gcc --version
```

rpm 是一个包管理工具

```
rpm -qa pcre pcre-devel zlib zlib-devel openssl openssl-devel
```

