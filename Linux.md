# Linux

### 1.Centos安装

```shell
# 挂载引导盘xfs(1G) 挂载主要起到一个隔离的作用 该目录下的文件都会写到一个分区
/boot 
# 虚拟内存swap(与内存大小一致)
swap

# 个人用户不开启kdump

# 设置主机名和开启网卡
```

### 2.终端（centos）

```shell
# 打开真正的终端
ctrl+alt+F2
# 输入用户和密码

# 退出到图形界面
ctrl+alt+F1

# 用户@主机名 (超级用户是"#",普通用户是"$") ~是当前用户的家目录
[user@localhost ~]$ 
```

### 3.根目录说明

* bin (文件夹链接，指向usr/bin)：直接可以执行的常用命令(binary)
* sbin (文件夹链接，指向usr/sbin): 系统级别的可执行常用命令
* lib (文件夹链接，指向usr/lib): 共享库库目录（相当于dll文件）
* lib64  (文件夹链接，指向usr/lib64): 共享库库目录（相当于dll文件）
* usr (所有用户级别的东西，涵盖了二进制文件，各种文档，各种头文件，x，还有各种库文件；还有诸多程序，例如 ftp，telnet 等等)
* boot (引导目录)
* dev (设备目录)
* etc (系统管理所需要的配置文件)
* home (每一个普通用户的主目录)
* root (root用户的主目录)
* opt (可选目录，给第三方软件包)
* media (存放可移动的媒体设备:U盘，光驱)
* mnt (挂载目录，外部存储)
* proc (进程目录，现有硬件和当前进程的信息)
* run (当前系统运行以来的所有临时信息，临时文件，重启删除)
* srv (系统服务相关)
* sys (系统硬件信息)
* tmp (临时目录)
* var (可变目录，存放可能会变化和修改的东西，如log  3)

### 4.Vim编辑器（有颜色界面）

*linx自动补全：table*

vim中的编辑模式

![vim编辑图片](D:\Desktop\md文件\img\linux\vim模式.png)

##### 一般模式

```shell
# 一般模式
# 复制当前的一行
yy 
# 粘贴一行 粘贴三行
p / (3p)
# 复制多行
3yy
# 删除当前行
dd
# 删除多行
3dd
# 撤销操作
u
# 从当前光标复制到当前行结束
y$
# 从当前光标复制到当前行开始
y^
# 复制当前单词
yw
# 删除当前单词
dw
# 剪切
x/shift X
# 更改当前字符
r
# 进入替换模式
shift R
# 跳到下一个词
w
# 跳到上一个此
b
# 移动到文档开头
gg
# 移动到文档末尾
shift G
# 移动到指定行
3 shift G
```

##### 编辑模式

```shell
# 编辑模式
# 进入编辑模式
i
# 进入编辑模式并多加一行
o
```

##### 命令模式

```shell
# 命令模式
# 进入命令模式
:
# 显示行号
set nu
# 不显示行号
set nonu
# 保存
:w
# 保存并推出
:wq
# 强制退出
:q!
# 查找boot单词(跳转到第一个找到的boot)
/boot
# 跳转下一个
n
# 跳转到上一个
shift N
# 取消高亮显示
:noh 
# 把boot替换成booto(仅当前行匹配的第一个光标)
:s/boot/booto
# 把boot替换成booto(仅当前行匹配)
:s/boot/booto/g
# 把boot替换成booto(全部文档)
:%s/boot/booot/g
```

### 5.网络配置和系统管理操作

```shell
# 测试连接
ping www.baidu.com
# 查看ip
ifconfig
```

*NAT：主机应该访问不到虚拟机。主机和虚拟机的ip不在一个网段，因为主机通过一个NAT路由器又创建了一个局域网。主机和虚拟机在两个局域网中，虚拟机可以通过共享主机IP访问外部网络，外部网络无法访问虚拟机*

*解决方法：PC上虚拟一个网卡，该网卡可以接到当前虚拟子网的路由器上，这样PC就和虚拟机在同一个网段里面了。*

```shell
# 设置静态ip
vim /etc/sysconfig/network-scripts/ifcfg-ens33

    TYPE="Ethernet"
    PROXY_METHOD="none"
    BROWSER_ONLY="no"
    BOOTPROTO="static"
    DEFROUTE="yes"
    IPV4_FAILURE_FATAL="no"
    IPV6INIT="yes"
    IPV6_AUTOCONF="yes"
    IPV6_DEFROUTE="yes"
    IPV6_FAILURE_FATAL="no"
    IPV6_ADDR_GEN_MODE="stable-privacy"
    NAME="ens33"
    UUID="39000421-8cc1-4ac4-87c2-c57c42c7abaa"
    DEVICE="ens33"
    ONBOOT="yes"
    #IP
    IPADDR=192.168.206.131
    #wangguan
    GATEWAY=192.168.206.2
    #yuming
    DNS1=192.168.206.2
    
# 重启网络服务
service network restart
# 另一个网络服务
systemctl status NetworkManager
# 查看主机名
hostnamectl
# 更改主机名
hostnamectl set-hostname linux-study / vim /etc/hostname
# 在hosts中保存地址通讯录
vim /etc/hosts
# windows中的hosts
C:\Windows\System32\drivers\etc\hosts
```

### 6.Linux在中的服务和进程

*计算机中，一个正在执行的程序或命令，称为”进程“(process)。*

*启动之后一直存在，常驻内存的进程，一般被乘坐”服务“(service)。*

##### centos 6 版本之前(SysV)

```shell
service 服务名 start|stop|restart|status
```

##### centos 7(SystemD)

```shell
systemctl start|stop|restart|disable|enable|status 服务名
# 查看服务的方法
ls /usr/lib/systemd/system
```

#### linux的运行级别

![linux运行级别](D:\Desktop\md文件\img\linux\运行级别.png)

```shell
# 查看默认启动的系统级别
systemctl get-default
# 查看启动级别配置文件
vim /etc/inittab
# 设置默认启动的系统级别
systemctl set-default TARGET.target
# 直接切换 
init 3
init 5
# 查看所有开机自启动的服务
systemctl list-unit-files
# 查看防火墙状态
systemctl status firewalld

# 同步buffer到硬盘
sync
# 断电
poweroff
# 重启
reboot
# 关机重启命令(默认一分钟后，原因是linux是提前读，延迟写，有缓冲区)
shutdown
# 取消关机
shutdown -c
# 立刻关机
shutdown now 
# 三分钟关机
shutdown 3
# 某个时刻关机
shutdown 12:59
# 重启
shutdown -r
# 停机
shutdown -H
```

### 7.linux常用命令

*centos的默认shell：bash*

#### 1.帮助命令

```shell
# man获取帮助信息(空格向下翻页，f向上翻页)
man ls
# 查看内置命令(如cd exit history)
man -f cd
man 1p cd
# 查看命令类型
type 命令
# help获得内置命令的相关信息
help cd
```

#### 2.常用快捷键

```shell
# 停止进程
ctrl+c 
# 清屏
clear
# 提示
table键
# 查找执行过的命令
上下键
# 彻底清空
reset
```

#### 3.文件目录类

```shell
# 打印当前工作路径的绝对路径
pwd
# 列出文件信息
ls -a 
ls -l(ll)
# 创建文件夹
mkdir 文件夹名
# 创建连续目录
mkdir -p a/b/c
# 产出目录
rmdir b
rmdir -p a/b/c
# 创建文件(默认是文本文件)
touch a 
touch /home/user1/hello
# 复制文件
cp a acopy
cp a ../home/user1/
cp a ../home/user1/hello (覆盖)
# 递归复制文件(复制目录中的文件)
cp -r a b
# 删除(递归，强制，删除过程)
rm -r a
rm -f a
rm -v a
# 查看文件
cat a
more a (快捷键：空格 b = /关键字)
# 根据实际需要显示(比more更实际)
less a
# 回显
echo hello.world
# 回显(支持转义)
echo -e 文本
# 输出重定向
ls -l > 文件 (覆写到文件)
ls -al >> 文件 (追加到文件)
echo "123" >> 文件
# 查看当前环境变量
echo $
echo $PATH
echo $HOSTNAME
# 显示文件头部内容
head
head -n 5 文件
# 显示文件尾部内容
tail
# 最终文档所有更新变化
tail -f 文件
# 创建ln软连接
ln -s 路径命 路径名
# 删除软连接(这里这里的目录名千万不要加 / 否则删除的是真实地目录)
rm -rf link
# 创建硬链接
ln 路径命 路径名
# 查看已经执行的命令
history
# 重复调用命令
!(历史编号)
# 删除历史记录
history -c
```

#### 4.显示时间

```shell
# 显示当前时间
date
date +%Y
```

#### 5.用户管理命令

```shell
# 添加新用户
useradd 用户名
# 添加新用户到某个组
useradd -g 组名 用户名
# 指定主文件夹和名字(可以不一样)
useradd -d /home/dave david
# 更改用户的密码
passwd tony
# 验证用户存在
id tony
# 查看创建了哪些用户
cat /etc/passwd
# 切换用户
su 用户
# 删除用户(主文件夹还在)
userdel tony
# 查看自己(真实的自己,非跳转)
who am i
# 跳转的自己
whoami
# 决定哪些用户/组 可以临时使用sudo权限
vim /etc/sudoers
# 创建用户的时候回默认创建一个用户名称的组
# 查看组的信息
vim /etc/group
# 添加组
groupadd 组名
# 添加用户到组
usermod -g 组名 用户名
# 重命名组
groudmod -n 原名 新名

```

##### 用户权限

![用户权限](D:\Desktop\md文件\img\linux\用户权限.png)

```shell
# 更改文件权限(2进制方式)
chmod 777 文件名
chmod 741 文件名
# 修改文件使其所属组用户具有执行权限
chmod g+x 文件名
# 修改整个文件夹里面所有的文件
chmod -R 777 xiyou/
# 改变文件所属用户
chown 用户名 文件名
# 改变文件所属用户
chgrp 组名 文件名
```

#### 6.查找类

```shell
find 搜索范围 选项
find -name a
find /root/ -name a
find /root -name "*.cfg"
# 按用户找
find /home -user tony
# 按照大小找
find /root -size +2M

# 使用locate 按照数据库查(更快) 首先一定要updatedb
updatedb
locate tmp(只要包含tmp的文件)

# which查 命令文件 在哪
which ls
# whereis查 命令文件 在哪
whereis ls

# grep过滤查找以及"|"管道符
# "|" 表示将前一个命令的处理结果输出传递给后面的命令处理 
# 在文件中查找boot单词的位置 并统计出现行数，次数和字节大小
grep -n boot 文件名 | wc
ls | grep .cfg
```

#### 7.压缩解压类

```shell
# 压缩文件为.gz文件(不会保留原来的文件，会产生多个压缩包，只能压缩文件不能压缩目录)
gzip 文件
# 解压文件
gunzip 文件

# zip压缩(保留原来的源文件,可以打包压缩文件夹)
# 递归压缩
zip -r myroot.zip /root
# 解压
unzip -d /tmp myroot.zip

# tar打包成一个文件(没有压缩,可以选择压缩:z 打包:c 显示详细信息:-v 指定打包后的名字:-f 解包:-x 解压到指定目录:-C)
tar -zcvf tmp.tar.gz 文件名1 文件名2...
```

#### 8.磁盘管理类

```shell
# 查看当前目录结构
tree 
# 查看文件占用情况(只看表层目录)
ls -lh / 
# 统计文件大小
du 目录/文件
# 更合理的显示
du -h 目录/文件
# 不仅看目录大小，还要看所有文件
du -a 目录/文件
# 简略显示
du -sh
# 统计到某一层
du --max-depth=1 -ah

# 查看磁盘占用情况
df 选项
# 以更合理的形式展示
df -h

# 硬盘(SCSI/SATA)
/dev/sda3 
# 内存
tmpfs

# 查看内存使用情况
free -h

# 查看块存储设备
lsblk
lsblk -f

# 挂载设备
mount /dev/cdrom /mnt/cdrom/
# 取消挂载
umount /dev/cdrom

# 分区操作
# 查看磁盘信息
fdisk -l
# 插入新的硬盘
# 重启
reboot
fdisk -l
# 对新硬盘进行分区(一个磁盘最多四个主分区，可以把最后一个分区划分成多个拓展分区)
fdisk /dev/sdb
# 分区操作 m p w
# 指定文件系统格式化
mkfs -t xfs /dev/sdb1
# 挂载位置
mount /dev/sdb1 /home/user1
# 查看磁盘使用情况
df -h
```

#### 9.进程管理类

*守护进程：往往都有一个d结尾*

```shell
# 查看用户前台进程
ps
# 查看系统中所有的进程 a:带有终端的 x:所有进程，包括没有终端的 u:友好的风格
# TTY:终端(tty2-tty6是本地的字符界面终端，pts/0-255代表虚拟终端)
# VSZ:该进程占用的虚拟内存的大小
# RSS:该进程占用的实际物理内存的大小
# STAT:进程状态。S:睡眠 R:运行 T:暂停状态 Z:僵尸状态 s:包含子进程
ps aux | grep xxx
# 查看系统中所有进程(可以查看父子进程之间的关系) -f:完整格式 -e:所有进程
# PID PPID是父进程的PID
ps -ef | grep xxx
# 终止进程
kill 进程号
# 强迫进程立刻停止
kill -9 进程号
# 某一名字的进程全部终止
killall sshd
# 查看进程树 
pstree
# 显示进程的PID 显示进程的所属用户
pstree -p -u

# top实时监控进程状态
# 排序方式 按照内存:shift M 按照CPU:shift P 进程号:shift N
# 退出 q
top
# 不显示闲置和僵死的进程
top -i
# 仅监控某个进程
top -p 进程号

# netstat显示网络状态和端口占用信息 a:所有套接字 n:拒绝显示别名 p:表示哪个进程在调用
netstat -anp | less

# crontab系统定时任务
# 重启crond服务
systemctl restart crond
# 查询所有定时任务
crontab -l
# 创建定时任务
crontab -e
# 删除定时任务
crontab -r
```

#### 10.软件包管理

```shell
# rpm
# 查询已经安装的rpm软件包
rpm -qa
rpm -qa | grep xxx
# 查询展示详细信息
rpm -qi xxx
# 卸载
rpm -e xxx
# 安装(根据.rpm安装包安装)
rpm -ivh xxx

# yum(基于rpm,类似maven)
# 查看已经安装的软件
yum list 
yum list | grep xxx
# 安装
yum -y install xxx
# 删除
yum remove xxx
# 修改yum源
vim /etc/yum.repo.d/CentOS-Base.repo
```

### 8.Shell编程

#### 1.shell基本

*Shell是一个命令行解释器，它接受应用程序/用户命令，然后调用操作系统内核*

*Shell还是一个功能相当强大的编程语言，易编写，易调试*

```shell
# 查看所有的shell
cat /etc/shells
# 查看默认shell
echo $SHELL
```

```shell
# shell编程
# 脚本以#!bin/bash开头(指定解析器)

#!bin/bash
echo "hello,world"
```

```shell
# 方法1和2都是启动并嵌套了一个子shell进程，要退出的话使用exit，3并不启动新的子shell,在当前环境中一行一行启动解析
# 执行脚本(1)
sh或者bash xxx.sh
# 执行脚本(2)这种方法要考虑权限
xxx.sh或./xxx.sh
# 执行脚本(3)
source xxx.sh
. xxx.sh
```

#### 2.变量

```shell
# $HOME $SHELL $USER $PWD $PATH
# 查看全局环境变量
env | less
# 所有变量
set | less

# 自定义变量(看作字符串，无法字符计算)
变量名=变量值 (注意前后不能有空格)
a=2
echo $2
# 局部变量导出成全局变量
export xxx
# 在子shell中更改就只在子shell中有效，export也没用
# 子shell在脚本中也无法运行局部变量 
# 只读变量
readonly b=5
# 撤销变量,不能unset只读变量
unset a

# 特殊变量
$n(功能描述: n为数字,$0代表该脚本名称,$1-$9代表第一到第九个参数,10以上的参数,十以上的参数用大括号包含,如${10})
$#(功能描述: 获取输入参数的个数)
$*(获取所有参数，并看作一个整体)
$@(获取所有参数，看作一个数组)
$?(返回最后一次执行命令的返回状态，如果为0，代表上一个命令执行正确，如果不为0，证明上一个命令执行不正确了)

#!bin/bash
echo "hello,$1"

# 使用脚本
./hello.sh xiaoming
./hello.sh xiaoliang

# 示例(单引号内的内容只看作字符串)
echo '========$n========'
echo script name:$0
echo 1st paramater:$1
echo 2nd paramater:$2
echo '========$#========'
echo paramater number:$#
echo '========$*========'
echo $*
echo '========$*========'
echo $@

#shell中
echo $?
```

#### 3.运算符

```shell
# 注意要带空格
expr 1 + 2
expr 1 - 2
expr 5 \* 2

# 运算符方法
echo $[5*2]
echo $((5*2))
a=$[6+8]

# 命令替换
a=$(expr 1 \* 2)
```

#### 4.条件判断

```shell
# 判断a的值是否是hello,0是true,1是false
test $a = hello 
echo $?

# 运算符条件判断(一定要有空格,linux中没有> < lt:less than gt:great than)
[ $a = Hello ]
[ $a != Hello ]
[ 2 -lt 3 ]
[ 2 -gt 3 ]
echo $?

# 判断文件的权限
[ -r hello.sh]
[ -w hello.sh]
# 判断文件是否存在
[ -e /home/user1/info ]
# 是否是文件 是否是目录
[ -f /home/user1/info ]
[ -d /home/user1/info ]

# 多条件判断(&&表示前一条执行成功时,才执行后一条,||表示上一条失败后，才执行吓一条)
[ user1 ] && echo OK || echo notOK
[ user1 ] && echo "15<20"|| echo "15>20"
```

#### 5.流程控制

```shell
if [ 条件判断式 ];then
	程序
fi

# 多分枝
if [ 条件判断式 ]
then
	程序
elif [ 条件判断式 ]
then
	程序
fi

# 示例
if [ $a -gt 18 -a $a -lt 35 ];then echo OK;fi

# 多分枝
case $变量名 in
"值1")
	程序1
;;
"值2")
	程序2
;;
*)
	默认模式
;;
esac

# for循环((中可以直接用运算符> <))
for (( 初始值;循环控制条件;变量变化 ))
do
	程序
done

for 变量 in 值1 值2 值3
do 
	程序
done

# while循环
while [ 条件判断式 ]
do
	程序
done

# 示例
for (( i=1;i<=$1;i++ ))
do 
	sum=$[ $sum + $i ]
done

a=1
while [ $a -le $1 ]
do
	let sum2+=a
	let a++
done
echo $sum2
```

#### 6.read读取控制台输入

```shell
# -t 输入时候的等待时间，如果没有-t就一直等待 
# -p 输入时候的提示符

#!bin/bash
read -t 10 -p "请输入您的名字:" name
echo "welcome,$name"
```

#### 7.函数

```shell
# 系统函数
# basename获取文件的名称
basename /home/user1/banzhang.txt
# 同时去掉后缀
basename /home/user1/banzhang.txt .txt

# dirname获取文件之前的绝对路径
dirname /home/user1/banzhang.txt

# 示例(单引号内的内容只看作字符串)
echo '========$n========'
echo script name:$(basename $0 .sh)
echo script path:$(cd $(dirname $0);pwd)
echo 1st paramater:$1
echo 2nd paramater:$2
echo '========$#========'
echo paramater number:$#
echo '========$*========'
echo $*
echo '========$*========'
echo $@

# 自定义函数 return后面只能跟(0~255)
#!/bin/bash
function add(){
	s=$[$1+$2]
	echo $s
}
read -p "请输入第一个整数:" a
read -p "请输入第二个整数:" b
add $a $b
```

