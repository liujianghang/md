# flask+gunicorn+docker+nginx

1.生成requirements.txt

2.代码上传到github

3.虚拟机创建文件夹，拉取代码

4.docker拉取相应python版本的image

5.docker挂载(多开几个端口)

```
docker run -id -p 5000:5000 -p 2222:22 --name=flask_login -v /root/flask_login:/root/flask_login python:3.11 /bin/bash
```

6.python换源

```
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

6.（容器中）创建虚拟环境

```
pip install virtualenv
cd my_project
virtualenv venv　　#venv为虚拟环境目录名，目录名自定义
source venv/bin/activate
# 下载相应的包
pip install -r requirements.txt
# flask_script的第十五行改一下 改成from flask_script._compat import text_type
```

