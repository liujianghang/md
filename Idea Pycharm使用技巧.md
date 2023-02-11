# Idea Pycharm使用技巧

### 1.JDK的配置情况

<u>*1.File > Project Structure > SDKs(JDK)*</u>

* IDE使用的JDK

![image-20230209203946454](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209203946454.png)



<u>*2.File > Project Structure > Project*</u>

* 当前项目使用的JDK版本和使用的语言level规范（尽量比配）。

* 当前编译的文件输出目录

![image-20230209204152325](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209204152325.png)

### 2.IDEA详细设置

* 设置打开时候是否默认上次关闭的项目

![image-20230209205211818](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209205211818.png)

* 取消自动更新（版本和插件）

![image-20230209205422742](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209205422742.png)

* 主题

  ![image-20230209205527452](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209205527452.png)

* 背景图

  ![image-20230209205703702](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209205703702.png)

* ctrl+滚轮改变字体

![image-20230209210729228](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209210729228.png)

* 更改注释颜色

  ![image-20230209211102774](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209211102774.png)

* 代码提示自动补全

  ![image-20230209211440342](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209211440342.png)

* 自动导包的设置

  ![image-20230209211558823](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209211558823.png)

* 设置项目的文件编码

  ![image-20230209211823838](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209211823838.png)

* 设置控制台编码

![image-20230209211937775](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209211937775.png)

* 设置类头的文档信息

  ![image-20230209212114178](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209212114178.png)

* 代码自动编译

  ![image-20230209212227646](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209212227646.png)

* 取消双击shift搜索（Ctrl+N）

![image-20230209212530449](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209212530449.png)

* 是否多行显示tab

![image-20230209212639995](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209212639995.png)

### 3.工程与模块管理

![image-20230209212755307](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209212755307.png)

<u>**工程本身也是一个module，不同的module可以选择不同的JDK**</u>

![image-20230209213327631](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209213327631.png)

<u>**Module可以被Remove为普通的文件包，下面的Java文件就无法执行了**</u>

![image-20230209214236232](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230209214236232.png)

**<u>讲普通文件夹升级成Module</u>**

### 4.代码模板

![image-20230211203636921](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211203636921.png)

**<u>后缀补全</u>**

**主要可以用到的实时模板和后缀补全：**

![image-20230211204149080](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211204149080.png)

![image-20230211204233642](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211204233642.png)

![image-20230211204438719](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211204438719.png)

![image-20230211204527944](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211204527944.png)

![image-20230211204724567](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211204724567.png)

### 4.快捷键

![image-20230211205014684](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211205014684.png)

![image-20230211205049751](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211205049751.png)

![image-20230211205155834](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211205155834.png)

![image-20230211205353272](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211205353272.png)

![image-20230211210012180](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211210012180.png)

![image-20230211210217286](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211210217286.png)

**<u>如何查找自己想要得的快捷键？</u>**

![image-20230211210446274](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211210446274.png)

### 5.断点调试

步骤：

* 添加断点（打的断点是刚走到，但是还没有执行）
* 启动调试
* 单步执行
* 观察变量和执行流程，找到并解决问题

![image-20230211211823370](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211211823370.png)

<u>**打上断点后的图标含义**</u>

* **行断点**

也就是最普通的断点

* **方法断点（对源码也适用）**

也就是打在方法上的断点，使用该方法或者退出该方法的时候都会运行到这里

如果方法被覆盖，会执行到覆盖的方法处，或者接口的实现处。

* **字段断点**

也就是在类的属性声明上打断点，默认对属性的修改操作进行监控（还可以设置成对属性的访问进行监控）

* **条件断点（可以设置断点什么时候起作用）**

![image-20230211215134047](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211215134047.png)

* **条件断点（可以设置断点什么时候起作用）**

对异常进行跟踪，如果程序出现异常，程序就会执行，自动停住，在断点结构界面添加

![image-20230211215635851](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211215635851.png)

* 针对某一线程进行调试（其他线程的运行不会受到影响）

  ![image-20230211220149037](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211220149037.png)

* 强制结束（让代码不以正常模式走完）

  ![image-20230211220413598](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211220413598.png)

* 自定义Debug界面的显示视图

![image-20230211220759755](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211220759755.png)

调整试图

![image-20230211220818257](C:\Users\11782\AppData\Roaming\Typora\typora-user-images\image-20230211220818257.png)

### 6.IDEA常用插件

* Alibaba Java Coding Guidelines（企业代码规范检查）
* jclasslib Bytecode Viewer（查看字节码文件信息）
* Translation（翻译软件）
* GenerateAllSetter（自动生成所有的设置，获取方法）
* JavaDoc（右键直接生成文档注释）
* Material Theme UI（UI市场）
