# python的跨模块导入

### 1.关于包(package)

一个包就是一个文件夹，但该文件夹下必须存在**init**.py 文件, 该文件的内容可以为空，**int**.py用于标识当前文件夹是一个包。

这个**init**.py的文件主要是用来对包进行一些初始化的，当当前这个package被别的程序调用时，**init**.py文件会先执行，一般为空。

### 2.跨模块导入

目录结构如下

```
my_proj
├── apeland_web
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── manage.py
└── my_proj
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

根据上面的结构，如何实现在apeland*web/views.py里导入my*proj/settings.py模块？

路径找不到，二者不在同一文件夹路径下。

答案是**添加环境变量，把父亲级的路径添加到sys.path中，就可以了，这样导入 就相当于从父亲级开始找模块了。**

***在apeland_web/views.py中添加环境变量***

```
import sys ,os
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
 #__file__的是打印当前被执行的模块.py文件相对路径，注意是相对路径
print(BASE_DIR) # 输出是/Users/alex/PycharmProjects/apeland_py_learn/day4_常用模块/my_proj 
sys.path.append(BASE_DIR)
from  my_proj import settings
print(settings.DATABASES) 
```

**官方推荐的跨目录导入方法**
虽然通过添加环境变量的方式可以实现跨模块导入，但是官方不推荐这么干，因为这样就需要在每个目录下的每个程序里都写一遍添加环境变量的代码。
**官方推荐的玩法是，在项目里创建个入口程序，整个程序调用的开始应该是从入口程序发起，这个入口程序一般放在项目的顶级目录，python从哪里开启 当前的环境变量中的工作**
这样做的好处是，项目中的二级目录 apeland_web/views.py中再调用他表亲my_proj/settings.py时就不用再添加环境变量了。
原因是由于manage.py在顶层，manage.py启动时项目的环境变量路径就会自动变成….xxx/my_proj/这一级别。

