## 0. 安装Django

```shell
pip install django
```

## 1. 新建项目

注：我的Pycharm为社区版，创建项目时，File->New Project... 显示为：

![newproject](<https://zsy0216.coding.me/image/csdn/django/newproject.png>)

​	注意勾选内容。

<u>如果你的Pycharm版本为其他版本，可以直接创建Django项目，下面的内容是在社区版的基础上完成的。</u>

### 1.1 创建Django项目

Pycharm社区版创建完项目，只需要执行以下步骤即可创建Django项目。

* 打开Terminal输入以下命令

  `django-admin startproject projectname`

   ![terminal](<https://zsy0216.coding.me/image/csdn/django/terminal.png>)

* 此时的目录结构：

   ![structure1](<https://zsy0216.coding.me/image/csdn/django/structure1.png>)

### 1.2 新建项目下的App

* 到Django项目下，输入以下命令

​	`python manage.py startapp appname`

![newapp](<https://zsy0216.coding.me/image/csdn/django/newapp.png>)

* 此时的目录结构：

![structure2](<https://zsy0216.coding.me/image/csdn/django/structure2.png>)

### 1.3 创建templates文件夹

此目录下即用来存放我们的html文件。

此目录一般是与app的主目录是平级的。当然也可以建立在app的目录下，主要取决于你的模板，一般是全局使用呢，还是专门服务于某个app。

* 新建`templates`文件

![newtemplates](https://zsy0216.coding.me/image/csdn/django/newtemplates.png)

* 配置templates文件路径

打开项目目录下的setting.py文件，找到TEMPLATES，如图示：

![templatesdir](https://zsy0216.coding.me/image/csdn/django/templatesdir.png)

### 1.4 启动项目

* 输入以下命令：

​	`python manage.py runserver`

![runapp](<https://zsy0216.coding.me/image/csdn/django/runapp.png>)

点击上图红色圈中的内容，浏览器弹出如下页面表示创建成功。

![runsucess](<https://zsy0216.coding.me/image/csdn/django/runsucess.png>)

**==另一种启动方式，通过配置Pycharm Edit Configuration==**

![run2.1](<https://zsy0216.coding.me/image/csdn/django/run2.1.png>)

 输入`runserver 8000`	,8000为项目访问端口；

![run2.2](<https://zsy0216.coding.me/image/csdn/django/run2.2.png>)

![run2.3](<https://zsy0216.coding.me/image/csdn/django/run2.3.png>)

## 2. Django项目连接数据库

### 2.1 创建数据库

本次项目的测试数据库为mysql，使用可视化软件Navicat。

新建一个数据库，取名为testDjango。

![newdatabase](<https://zsy0216.coding.me/image/csdn/django/newdatabase.png>)

### 2.2 给项目导入mysql驱动

在File->Setting->Project:testDjango->Project Interpreter下按照图示添加PyMySQL驱动；

![mysqldriver](<https://zsy0216.coding.me/image/csdn/django/mysqldriver.png>)

### 2.3 配置数据库

* 找到工程目录下的`settings.py`文件，并打开，找到DTABASE,按照图示进行修改

![databasesetting](<https://zsy0216.coding.me/image/csdn/django/databasesetting.png>)

即在`setting.py`中添加上以下代码：

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',  # 数据库驱动
        'NAME': 'testDjango',  # 数据库名
        'USER': 'root',  # 用户名
        'PASSWORD': '',  # 密码，本机mysql数据库没有设置密码，故此处为空，此处应修改为自己的数据库密码
        'HOST': '127.0.0.1',  # 主机ip
        'PORT': '3306'  # 数据库端口号
    }
}
```

### 2.4 项目加载数据库

* 找到工程目录下的`__init__.py`文件，并打开，添加以下代码：

```python
import pymysql

pymysql.install_as_MySQLdb()
```

![initpy](<https://zsy0216.coding.me/image/csdn/django/initpy.png>)

* 运行项目，此时运行项目会出现错误，按照以下步骤纠错即可完成数据库的连接；

  此处错误原因是`Django`项目版本与`mysql`驱动版本不匹配的问题，按照图示注释掉指定代码即可：

![mysqlversion](<https://zsy0216.coding.me/image/csdn/django/mysqlversion.png>)

* 继续运行，会出现以下错误，继续按照图示修改即可：

```python
query = query.encode(errors='replace')
```

![encode](<https://zsy0216.coding.me/image/csdn/django/encode.png>)

* 此时再运行项目，出现图示内容即代表Django项目连接数据库成功！

![mysqlsucess](<https://zsy0216.coding.me/image/csdn/django/mysqlsucess.png>)

### 2.5 创建数据库表模型

* 找到app目录下的`models.py`，并作图示内容修改：

![models](<https://zsy0216.coding.me/image/csdn/django/models.png>)

```python
from django.db import models


# Create your models here.  User对应数据库中的表
class User(models.Model):
    # 如果没有的话，默认会生成一个名称为id的列，如果要显示的自定义一个自增列
    id = models.AutoField(primary_key=True)
    # 类里面的字段代表数据表中的字段(username)，数据类型则由CharField（相当于varchar）
    username = models.CharField(max_length=100)
    # 数据库表中的密码字段
    password = models.CharField(max_length=100)
```

`model.py`文件中`User`类对应数据库中的数据表，属性对应表中的字段名称；

一般表的命名规则为：app名称_表名称，例如本示例中的`myapp_user`

* 向数据库中同步创建表并同步数据：

  * 创建同步文件，在Terminal中输入以下命令：

    `python manage.py makemigrations`

  输入命令可能出现下图示提示，无法同步，

  ![makemigration](<https://zsy0216.coding.me/image/csdn/django/makemigration.png>)

  这时需要在项目目录下的`setting.py`文件中的`INSTALLED_APPS`中追加以下内容：

  ```python
      'myapp.apps.MyappConfig',
  ```

  ![appsetting](<https://zsy0216.coding.me/image/csdn/django/appsetting.png>)

  之后重新输入命令：`python manage.py makemigrations` 出现图示内容表示同步成功

  ![migrations](<https://zsy0216.coding.me/image/csdn/django/migrations.png>)

  * 创建表结构并同步到数据库，输入以下命令：

    `python manage.py migrate`

  * 打开数据库，看到数据库中新增了许多表，其中的User类生成的表（myapp_user）是你需要操作的表，其他都是框架自动生成的表。

  ![tablemigrate](<https://zsy0216.coding.me/image/csdn/django/tablemigrate.png>)

## 3. 实现显示数据库表数据功能

### 3.1 编写业务逻辑代码

* 在app目录下打开`view.py`，输入以下内容：

```python
from django.shortcuts import render
from myweb.myapp.models import User


# Create your views here.

# 页面查询用户信息
def queryUsers(request):
    # 到数据库查询所有用户信息
    user = User.objects.all()
    # 将数据发给页面
    context = {"userList": user}
    return render(request, "userInfo.html", context)
```

![viewusers](https://zsy0216.coding.me/image/csdn/django/viewusers.png)

### 3.2 编写前端显示界面

* 在`templates`目录下新建`view.py`中声明好的`userInfo.html`文件

```html

```



* 