## 1. 实现显示数据库表数据功能

### 1.1 编写业务逻辑代码

- 在app目录下打开`view.py`，输入以下内容：

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

### 1.2 编写前端显示界面

- 在`templates`目录下新建`view.py`中声明好的`userInfo.html`文件

```html

```



- 