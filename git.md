## git

*everything is local.*

### 1.Git的提交机制

![image-20230201190145256](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230201190145256.png)

### 2.代码托管中心

**局域网**

* Gitlab （不开源）

**互联网**

* GitHub
* Gitee

### 3.Git安装

### 4.Git常用命令(table键补全)

**1.git设置用户签名(只用设置一次)**

作用是区分不同操作者的身份,Git首次安装之后必须要设置签名,这个用户签名和将来登录Github没有任何关系.

```shell
$ git config --global user.name 用户名
```

```shell
$ git config --global user.email 邮箱(虚拟邮箱)
```

在C:\Users\用户\\.gitconfig中可以查看已经登陆的信息

```
[user]
	name = Luna
	email = Luna@qq.com
```

**2.初始化**

在项目目录中:

```shell
$ git init
```

**3.查看本地库状态**

```shell
$ git status
```

在工作区创建了文件后:

```shell
$ vim hello.txt
```

```shell
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        hello.txt

nothing added to commit but untracked files present (use "git add" to track)

```

**4.添加暂存区(自动改变文件换行符)**

```shell
$ git add hello.txt
warning: in the working copy of 'hello.txt', LF will be replaced by CRLF the next time Git touches it
```

```shell
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage) // 删除在暂存区的方法
        new file:   hello.txt
```

**5.提交本地库(形成历史版本)**

```shell
$ git commit -m "日志信息" hello.txt
warning: in the working copy of 'hello.txt', LF will be replaced by CRLF the next time Git touches it
[master (root-commit) 971e221] first commit
 1 file changed, 13 insertions(+)
 create mode 100644 hello.txt
```

```shell
$ git status
On branch master
nothing to commit, working tree clean
```

**查看简单log**

 ```shell
 $ git reflog
 971e221 (HEAD -> master) HEAD@{0}: commit (initial): first commit
 ```

**查看log**

```shell
$ git log
commit 971e22179925f43fbed40dac37209ea7b1f046ce (HEAD -> master)
Author: Luna <Luna@qq.com>
Date:   Sat Feb 4 11:13:21 2023 +0800

    first commit

```

**6.修改文件**

```shell
$ vim hello.txt
```

修改了文件后再次查看状态

```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

再次add和commit

```shell
$ git add hello.txt
$ git commit -m "second commit" hello.txt
warning: in the working copy of 'hello.txt', LF will be replaced by CRLF the next time Git touches it
[master b32a7f7] second commit
 1 file changed, 7 insertions(+)  // 七行增加
```

再次查看简单log

```shell
$ git reflog
b32a7f7 (HEAD -> master) HEAD@{0}: commit: second commit
971e221 HEAD@{1}: commit (initial): first commit
```

 **6.版本穿越(回到原来的版本)**

先查看版本信息

```shell
$ git reflog
ef29bef (HEAD -> master) HEAD@{0}: commit: third commit
b32a7f7 HEAD@{1}: commit: second commit
971e221 HEAD@{2}: commit (initial): first commit
```

记住版本号,退回到原来版本

```shell
$ git reset --hard b32a7f7
HEAD is now at b32a7f7 second commit
```

再次查看版本号

```shell
$ git reflog
b32a7f7 (HEAD -> master) HEAD@{0}: reset: moving to b32a7f7
ef29bef HEAD@{1}: commit: third commit
b32a7f7 (HEAD -> master) HEAD@{2}: commit: second commit
971e221 HEAD@{3}: commit (initial): first commit
```

此时文件版本已经回到原来的状态

![image-20230204113721876](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230204113721876.png)

### 5.Git分支操作

#### 公司运行模式:

![image-20230204113807351](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230204113807351.png)

**分支**：版本控制过程中，为了同时推进多个任务，为每个任务，创建单独的分支，可以从主线开发上分离开来。形成一个单独的副本。

![image-20230204114052860](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230204114052860.png)

**好处：**同时并行多个功能开发，如果一个功能失败，不会对其他功能产生影响。

**1.查看分支**

```shell
$ git branch -v
* master b32a7f7 second commit
```

**2.创建分支**

```shell
$ git branch hot-fix
```

再次查看分支

```shell
$ git branch -v
  hot-fix b32a7f7 second commit
* master  b32a7f7 second commit
```

**3.切换分支**

```shell
$ git checkout hot-fix
Switched to branch 'hot-fix'
```

再次查看分支

```shell
$ git branch -v
* hot-fix b32a7f7 second commit
  master  b32a7f7 second commit
```

修改文件

```shell
$ vim hello.txt
```

```shell
$ git add hello.txt
$ git commit -m "hot-fix first commit" hello.txt
[hot-fix 4733136] hot-fix first commit
 1 file changed, 1 insertion(+), 1 deletion(-)
```

**4.合并分支**

**切换回master分支(!!)**

```shell
$ git checkout master
Switched to branch 'master'
```

合并分支

```shell
$ git merge hot-fix
Updating ef29bef..4733136
Fast-forward
 hello.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)

```

查看文件状态已经改变(正常合并)

```shell
$ vim hello.txt
```

**5.冲突合并**

合并分支时，两个分支在同一个文件的同一个位置有两套完全不同的修改，Git无法决定使用哪一个，

必须人为决定新内容。

**举例说明**

修改master分支

```shell
$ vim hello.txt

Git hello! master text!
```

```shell
$ git add hello.txt
$ git commit -m "master txt" hello.txt
```

切换到hot-fix分支

```shell
$ git checkout hot-fix
```

```shell
$ vim hello.txt

Git hello! hot-fix txt!
```

```shell
$ git add hello.txt
$ git commit -m "hot-fix txt" hello.txt
```

切换到master分支

```shell
$ git checkout master
```

**合并(产生代码冲突)**

```shell
$ git merge hot-fix
Auto-merging hello.txt
CONFLICT (content): Merge conflict in hello.txt
Automatic merge failed; fix conflicts and then commit the result.

$ 11782@Luna MINGW64 /d/Projects/git-demo (master|MERGING)
```

查看状态

```shell
$ git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

**手动打开文件**

```shell
vim hello.txt

Git hello!
<<<<<<< HEAD
Git hello! master text! // 当前分支代码
=======
Git hello! hot-fix txt!  // 要合并的代码
>>>>>>> hot-fix
```

手动合并(特殊符号都删掉，保留需要的内容)：

```
Git hello! master text! hot-fix txt!
```

再次**add  commit(不能带文件名！！)**

```shell
$ git commit -m "merge"
[master 6591e6c] merge
```

成功合并，但hot-fix分支仍是原来的。

### 6.团队协作

#### 团队内协作

![image-20230204121735132](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230204121735132.png)

#### 跨团队协作

![image-20230204122049922](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230204122049922.png)

### 7.Gitlab

**1.创建库**

有HTTPS和SSH两种链接

```shell
https://github.com/liujianghang/git-demo.git
```

```
git@github.com:liujianghang/git-demo.git
```

**2.基本语法**

**查看当前所有远程地址别名**

```shell
git remote -v 
```

**增加别名(不要用http，用ssh)**

```shell
git remote add git-demo git@github.com:liujianghang/git-demo.git
```

**删除别名**

```
git remote remove git-demo
```

**再次查看**

```shell
$ git remote -v
git-demo        git@github.com:liujianghang/git-demo.git (fetch)
git-demo        git@github.com:liujianghang/git-demo.git (push)
```

**3.推送到远程库（最小单位是分支）**

```shell
$ git push git-demo master
```

![image-20230206104538864](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230206104538864.png)

**4.拉取远程库**

当发现远程库被修改后与本地有所不同时，采用拉取

![image-20230206104627875](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230206104627875.png)

```shell
$ git pull git-demo master
```

让本地库与远程同步

**5.克隆远程库**

创建一个新的文件夹

```shell
$ git clone git@github.com:liujianghang/git-demo.git
```

直接克隆（所有文件和.git文件）

clone会做三件事

* 拉取代码

* 初始化本地库

* 创建别名（origin）

### 8.团队内协作

别人如果想和你同时开发一个项目，共同推送代码到远程库。

需要在github上和你加入到同一个组里面（单位是一个库）你

**setting——Collaborators——Add people**

填写需要添加的人的账号

**复制邀请函给对方，对方复制链接进入页面，接受邀请函**

### 9.跨团内协作

团队外的人的视角：

**复制链接进入到库——右上角Fork——修改代码并上传——Pull requests——New pull request——Create pull request——编辑相关的话**

团队内的人的视角：

**pull request——审核代码——确认无误后Merge pull request**

### 10.SSH免密登录

生成密钥(-C 是描述符)

```shell
$ ssh-keygen -t rsa -C 1178262698@qq.com
```

连续敲回车。

之后在**用户/.ssh**会产生

* id_rsa
* id_rsa.pub

**复制id_rsa.pub中的密钥**

```shell
$ cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC4U2gycMbGQDHEGBC+hp+1JKNy9eHwOtbx//JYGXWNN08KESoVrwG0g3JkLg0OtUHWsM0Wp7av8U8WqWh+A/Ag7VgyntgHpGkx2rmcN+BPxyW3jrdnNCGS8dAURGmf62/iQjCAzPp1AUz4NXpM0KBnfnqTCTBv7R0XSHLDhMKeUJm6pe0Q/oNk3XAPMuYS9joDH8P5iQyM/SbL1/pzZX7cfm44IHo7Jdrpn01UKxpX9Du+VH51WavsiyfmtENGruwF22ZARGnQLKeWWSwJMVCmx+OdW1e9mcc9nHawhMBrkH9q82taPf87NT5v6L6RlYFEY3HXjfbpsypO3MjDhXMp94A9wqySHQ7qEwsWqZ3PHdRbDPjXCxoVu8NtPSuHD/02gtvM4cW99dQaKnXuw0K8GM8NNEaRZFCiQSYFYbvdRoW5eHCW7nZ0ZJg+o+1ZB95+/w27jfDZwR/JvMfXqUtSYtJi4e6+WG1lsVRsXNVihD/oZZzIQ5hodjdr/99TI5U= 1178262698@qq.com

```

**github——settings——SSH and GPG keys——New SSH key——添加密钥**

*但是由于代理的问题，可能需要进行如下操作*

详细解决方法如下：

[github 配置了公钥依旧提示git@github.com‘s password: Permission denied, please try again. 的解决办法_XeonYu的博客-CSDN博客](https://blog.csdn.net/yuzhiqiang_1993/article/details/127032178)

按照文档提示在config文件中添加以下内容,没有config的话自己新建一个(就在你的.s[sh文件](https://so.csdn.net/so/search?q=sh文件&spm=1001.2101.3001.7020)夹内)，把后缀名删掉即可。

```
Host github.com
Hostname ssh.github.com
Port 443
User git
```

最后复制链接，在本地测试是否成功

```shell
$ git pull git@github.com:liujianghang/git-demo.git master
The authenticity of host 'github.com (223.75.236.241)' can't be established.
RSA key fingerprint is SHA256:rEmlJenVMSL5GVemSY0Gk8WGw6B4ege4J85M+vup8R0.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])?

yes
```

*tip:合并两个完全不相干的分支：*

```shell
$ git pull origin master --allow-unrelated-histories
```

### 11.IDEA集成使用Git

**1.在IDEA中选择Git程序**

**settings——Version Control——Git——配置安装目录**

![image-20230206122956210](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230206122956210.png)

**2.让项目被Git管理（VCS）**

![image-20230206123422821](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230206123422821.png)

**3.红色文件代表未被add，绿色文件代表未被commit，蓝色代表被追踪过，然后被修改，没有颜色代表已经被commit，棕色代表被忽略的**

![image-20230206124747884](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230206124747884.png)



**4.创建.gitignore，加入不想提交的文件和后缀**

**5.切换版本(黄色标签是当前的版本，绿色标签是当前分支)**

在页面的左下角：不仅可以看到本地的分支和版本记录，还可以看到远程的版本

![image-20230206125008870](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230206125008870.png)

右键点击想要切换的版本进行切换

**checkout reversion \**\*\*\****

**6.创建/切换分支**

![image-20230206125625284](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230206125625284.png)

同样在页面的左下角，可以切换分支

**7.合并分支（正常）**

同样在页面的左下角，在当前分支，选择想要merge的分支，点击merge即可

**8.合并分支（冲突）**

产生冲突弹出窗口：

![image-20230206130551499](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230206130551499.png)

点击merge

![image-20230206130629699](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230206130629699.png)

左边是当前分支，中间是没有更改，右边是另外分支

### 12.IDEA集成使用Github

**1.登录账号**

**settings——Version Control——GitHub**

登录账号：

使用界面（推荐）/token登录。

**token登录：**

**Github——settings——Developer settings——Personal access tokens——Generate new token**

填写名字和权限（打满）

![image-20230206133706266](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230206133706266.png)

**口令只会显示一次！！**

**2.直接将项目传到Github上（直接创建仓库）**

**IDEA菜单——Git——GitHub——share project on GitHub——选择名字等——share**

**3.推送到远程库**

直接push**（push之前一定要先pull一下远程库的代码，然后更新后再push）**

**4.拉取远程库**

直接pullv

**IDEA菜单——Git——pull**

**5.克隆远程库**

![image-20230206140223233](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230206140223233.png)

![image-20230206140152174](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230206140152174.png)
