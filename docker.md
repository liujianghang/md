# docker

*Docker是一种容器技术，解决软件跨环境迁移的问题*

### 1.安装docker

```shell
# 1.yum包更新到最新
yum update
# 2.安装需要的软件包
yum install -y yum-utils device-mapper-persistent-data lvm2
# 3.设置yum源
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# 4.安装docker
yum install -y docker-ce
# 5.查看docker版本，验证是否安装成功
docker -v
```

### 2.配置docker镜像加速器

1.创建阿里云账号

搜索**容器镜像服务**——镜像工具——镜像加速器

可以得到**个人的加速器地址**和**全部的加速操作流程命令**

![image-20230208120227824](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208120227824.png)

2.直接复制命令粘贴到命令行

```shell
# 直接复制
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://hfg81c17.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

3.检查一下是否配置成功(这个文件就是加速地址)

```shell
cat /etc/docker/daemon.json
```

### 3.docker服务命令(daemon)

![image-20230208120748234](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208120748234.png)

```shell
# 1.启动docker服务
systemctl start docker
# 2.查看docker状态
systemctl status docker
# 3.停止docker服务
systemctl stop docker
# 4.重启docker服务
systemctl restart docker
# 5.开机启动docker服务
systemctl enable docker
```

### 4.docker镜像相关命令(image)

![image-20230208132626417](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208132626417.png)

```shell
# 查看镜像
docker images
# 查看镜像库
docker info
# 搜索镜像(与网络交互)
docker search redis
# 拉取镜像(需要去dockerhub找有什么版本,pull的时候多试几次)
# docker pull redis:3.2
docker pull redis
# 删除镜像
docker rmi de25a81a5a0b
docker rmi redis:lastest
```

*Error:拉取镜像的时候可能遇到的error：dial tcp: lookup registry-1.docker.io on 192.168.206.2:53: no such host*

```shell
# 更改DNS
sudo vi /etc/resolv.conf
# 将服务器名称改为google的服务器
nameserver 8.8.8.8
```

### 5.docker容器相关命令(container)

![image-20230208142314297](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208142314297.png)

```shell
# 创建并运行容器(退出terminal会自动关闭)
# -i：一直运行 -t：给容器分配终端 --name=名字：名字 /bin/bash:进入容器初始化命令(默认也是这个) 
# -it：交互式容器
docker run -it --name=c1 redis:latest /bin/bash
```

进入到了容器内部：

![image-20230208145547782](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208145547782.png)

```shell
# 退出容器(退出到宿主机)
exit
# 重启开启
docker start c1
```

```
# 查看正在运行的容器
docker ps
# 查看所有容器
docker ps -a
```

```shell
# 另一种方式创建容器
# -d:后台运行容器，不会自动关闭，守护式容器
docker run -id --name=c2 redis:latest
# 进入到容器
docker exec -it c2 /bin/bash
# 停止容器
docker stop c2
# 删除容器(开启的容器无法被删除)
docker rm c1
# 删除所有的容器(开启的容器无法被删除)
docker rm `docker ps -aq`
# 查看容器的信息
docker inspect c2
```

### 6.docker容器数据卷(解决数据交互，实现映射)

![image-20230208151230957](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208151230957.png)

![image-20230208151532611](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208151532611.png)

#### 1.配置数据卷实现交互

```shell
# 创建启动容器时，使用-v参数 设置数据卷
# docker run ... -v 宿主机目录(文件)：容器内目录(文件) ...
docker run -it --name=c1 -v /root/data:/root/data_container redis /bin/bash 
```

*1.目录必须是绝对路径*

*2.如果目录不存在，会自动创建*

*3.可以挂在多个数据卷*

```shell
# 连续挂载
docker run -it --name=c2 \
-v ~/date2:/root/data2 \
-v ~/data3:/root/data3 \
redis /bin/bash
```

#### 2.数据卷容器

![image-20230208153904171](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208153904171.png)

```shell
# 创建启动c3数据容器卷，使用-v参数，设置容器卷
# /volume:指的是容器目录，会在宿主机上分配一个目录(通过docker inspect查看) mounts中的source
docker run -it --name=c3 -v /volume redis /bin/bash
# 创建c1 c2容器，使用--volumes-from c3参数 设置数据卷
docker run -it --name=c1 --volumes-from c3 redis /bin/bash
docker run -it --name=c2 --volumes-from c3 redis /bin/bash
```

### 7.docker应用部署

![image-20230208155536122](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208155536122.png)

#### 1.mysql部署

```shell
# 搜索mysql镜像
docker search mysql
# 拉取mysql镜像
docker pull mysql
# 创建目录
mkdir ~/mysql
cd ~/mysql
# 创建容器 $PWD:查看当前路径(宿主机)
# 配置，日志，数据 映射
# 环境变量(固定值)
docker run -id \
-p 3307:3306 \
--name=c_mysql \
-v $PWD/conf:/etc/mysql/conf.d \
-v $PWD/logs:/logs \
-v $PWD/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=biodwhub503 \
mysql

# 示例:
docker run -id -p 3307:3306 --restart=always --name=retro_mysql -–privileged -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=biodwhub503 mysql:8.0.32

# 进入到容器
docker exec -it c_mysql /bin/bash
# 进入到mysql
mysql -uroot -p123456
mysql>show databases;
mysql>create database db1;
mysql>use db1;

```

#### 2.使用Navicat外部链接(使用数据库密码：123456)

![image-20230208162327353](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208162327353.png)

#### 3.tomcat部署

```shell
# 搜索tomcat
docker search tomcat
# 拉取tomcat
docker pull tomcat
# 创建文件夹
mkdir ~/tomcat
cd ~/tomcat
# 创建容器，建立映射
docker run -id --name=c_tomcat \
-p 8080:8080 \
-v $PWD:/user/local/tomcat/webapps \
tomcat
```

#### 4.访问测试

![image-20230208164057449](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208164057449.png)

#### 5.nginx部署

```shell
# 搜索nginx
docker search nginx
# 拉取nginx
docker pull nginx
# 创建文件夹吗，用于存储nginx数据信息
mkdir ~/nginx
cd ~/nginx
mkdir conf
cd conf
# 在~/nginx/conf下创建nginx.conf文件，粘贴内容
vim nginx.conf
···
cd ~/nginx
# 创建容器，建立映射
docker run -id --name=c_nginx \ 
-p 80:80 \
-v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf
-v $PWD/logs:/var/log/nginx \
-v $PWD/html:/usr/share/nginx/html \
nginx
```

#### 6.redis部署

```shell
# 搜索redis
docker search redis
# 拉取redis
docker pull redis
# 创建容器，建立映射
docker run -id --name=c_redis -p 6379:6379 redis
# 外部访问redis
./redis-cli.exe -h 192.168.206.128 -p 6379
```

### 8.Dockerfile

![image-20230208165937318](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208165937318.png)

![image-20230208170204754](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208170204754.png)

![image-20230208170741502](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208170741502.png)

* *docker的本质：一个分层文件系统*
* *Centos的ios镜像文件包含bootfs和rootfs，而docker的centos镜像复用操作系统的bootfs，只有rootfs和其他镜像层，所以其镜像文件大小很小*
* *由于docker中镜像是分层的，tomcat虽然只有70Mb，但是他需要依赖父镜像和基础镜像（例如JDK），所以对外暴露的tomcat大小就为500Mb了*

#### 1.制作镜像

```shell
# 1.容器转为镜像(数据卷的文件不会写入,需要重新挂载)
docker commit af3 tomcat:my
# 2.镜像转为压缩文件
docker save -o tomcat_my.tar tomcat:my
# 3.装载压缩文件
docker load -i tomcat_my.tar
```

#### 2.Dockerfile

![image-20230208174844503](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208174844503.png)

 *dockerfile关键字：*

[Dockerfile常用命令（安全）_冰鑫999的博客-CSDN博客](https://blog.csdn.net/imouse728/article/details/125654297)

*例子:*

```dockerfile
FROM scratch
ADD centos-7-x86_64-docker.tar.xz /

LABEL \
    org.label-schema.schema-version="1.0" \
    org.label-schema.name="CentOS Base Image" \
    org.label-schema.vendor="CentOS" \
    org.label-schema.license="GPLv2" \
    org.label-schema.build-date="20201113" \
    org.opencontainers.image.title="CentOS Base Image" \
    org.opencontainers.image.vendor="CentOS" \
    org.opencontainers.image.licenses="GPL-2.0-only" \
    org.opencontainers.image.created="2020-11-13 00:00:00+00:00"

CMD ["/bin/bash"]
```

**假如要发布一个springboot项目**

```dockerfile
FROM java:8
MAINTAINER ljh@<1178262698@qq.com>
WORKDIR /
ADD springboot.jar app.jar
RUN yum install -y vim
CMD java -jar app.jar
```

**通过dockerfile构建镜像**

```shell
# docker build -f dockerfile文件目录 -t 镜像名称:版本
# 根据dockerfile创建对象
docker build -f ./springboot_dockerfile -t app:latest
# 创建容器
docker run -id -p 9000:8080 app
```

#### 3.服务编排

![image-20230208181018312](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208181018312.png)

![image-20230208181206104](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208181206104.png)

#### 4.Docker Compose

```shell
# 安装(多试几次)
curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
# 设置权限
chmod +x /usr/local/bin/docker-compose
# 查看版本信息
docker-compose --version
```

```shell
# 创建目录
mkdir docker-compose
cd docker-compose
# 固定写法
vim docker-compose.yml
```

*docker-compose.yml(该文件严格要求格式)*:

```docker
version:'3'
services:
	nginx:
		image: nginx
		ports:
		- 80:80
		links:
		- app
		volumes:
		- ./nginx/conf.d:/etc/nginx/conf.d
	app:
		image: app
		expose:
			- "8080"
```

```
# 创建./nginx/conf.d
mkdir -p ./nginx/conf.d 
# 创建app.conf文件
vim app.conf
```

*app.conf(app就是上面links中的app):*

```
server{
	listen 80;
	access_log off;
	
	location / {
		proxy_pass http://app:8080
	}
}
```

*启动：*

```
# 回到docker-compose.yml的目录
# 启动docker-compose
docker-compose up
```

### 9.docker与虚拟机对比

![image-20230208183806853](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230208183806853.png)