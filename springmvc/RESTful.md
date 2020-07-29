# 什么是RESTful

RESTFUL是一种网络应用程序的设计风格和开发方式，基于HTTP，可以使用XML格式定义或JSON格式定义。RESTFUL适用于移动互联网厂商作为业务使能接口的场景，实现第三方OTT调用移动网络资源的功能，动作类型为新增、变更、删除所调用资源。

**RESTFUL特点包括：**
1、每一个URI代表1种资源；

2、客户端使用GET、POST、PUT、DELETE4个表示操作方式的动词对服务端资源进行操作：GET用来获取资源，POST用来新建资源（也可以用于更新资源），PUT用来更新资源，DELETE用来删除资源；

# 实例说明

| CRUD | HTTP方法 | 举例    | 说明            |
| ---- | -------- | ------- | --------------- |
| 增   | POST     | /user   | 增加一个用户    |
| 删   | DELETE   | /user/1 | 删除id为1的用户 |
| 改   | PUT      | /user   | 修改用户        |
| 查   | GET      | /user/1 | 查询id为1的用户 |

传统风格中，如果要修改或删除时需要定义这样的url：`/updateUser?id=1`或`/deleteUser?id=1`，显然使用RESTful风格的url编码方式更加简洁，仅仅通过不同的HTTP方法就可以实施不同的业务逻辑。

# 怎么在SPringMVC中加入RESTful

这里为了方便，仅仅在springmvc环境中进行测试，不进行数据库的连接，只要访问不同方法的请求，在页面输出对应的消息即代表成功。

## 第一步，添加配置

在web.xml配置文件中增加一个过滤器:HiddenHttpMethodFilter

```xml
<filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

## 第二步，编写表单视图

这里需要在表单中添加一行隐藏域

```html
<input type="hidden" name="_method" value="DELETE">
```

## 第三步，controller方法

```java
@RequestMapping(value = "/user/{id}", method = RequestMethod.DELETE)
public String deleteOneUser(@PathVariable("id") Integer id, Model model) {
    model.addAttribute("users", "DELETE/user 删除id为 " + id + "的用户信息");
    return "restful";
}
```

