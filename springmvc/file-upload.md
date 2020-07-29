# Springmvc的文件上传

## 事前准备

### 1. 所需依赖

pom.xml

```xml
<!-- 此依赖包含commons-io -->
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>
```

### 2. 配置文件上传视图解析器

springmvc.xml

```xml
<!-- 配置上传文件拦截，设置最大上传文件大小，和最大内存大小 中文乱码等-->
<bean id="multipartResolver" name="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <!-- set the max upload size 100MB  100M=100*1024*1024(B)=104857600 bytes-->
    <property name="maxUploadSize" value="104857600" />
    <property name="maxInMemorySize" value="4096" />
    <!-- 处理文件名中文乱码 -->
    <property name="defaultEncoding" value="UTF-8" />
    <!-- resolveLazily属性启用是为了推迟文件解析，以便捕获文件大小异常 -->
    <property name="resolveLazily" value="true" />
</bean>
```

### 3. 文件上传页面

upload.jsp

```jsp
<%-- 表单提交方式为post，额外添加enctype属性为multipart/form-data 以二进制的形式传输数据 --%>
<form method="post" action="/do-upload" enctype="multipart/form-data">
    <input type="file" name="file"/>
    <input type="submit"/>
</form>
```

- 表单提交方式必须为`post`；
- 有`enctype`属性为`multipart/form-data`；
- `input`标签`type`属性为`file`；
- `name`和`controller`方法参数名相同，或和`@RequestParam`注解的`value`值相同；

### 4. 上传成功页面

success.jsp

```jsp
文件访问路径：<b>${requestScope.filepath}</b>- 上传成功~
<img src="${filepath}" align="right">

<h3>文件上传成功~</h3>
```

## 文件上传

### 上传代码逻辑

这里使用SpringMVC提供的MultipartFile接口，和commons-io提供的工具类完成。

```java
@RequestMapping(value = "/do-upload", method = RequestMethod.POST)
public String testFileUpload(MultipartFile file, HttpServletRequest request, Model model) throws IOException {
    if (!file.isEmpty()) {

        // 文件原始名称
        System.out.println("Process file: " + file.getOriginalFilename());

        //上传路径：webapp下的resources/files目录中
        String path = request.getServletContext().getRealPath("/resources/files");
        System.out.println(path);

        // 生成文件名，避免重复
        String filename = System.currentTimeMillis() + file.getOriginalFilename();

        //文件上传，将文件流拷贝到目标文件对象中
        FileUtils.copyInputStreamToFile(file.getInputStream(), new File(path, filename));
        //上传的第二种方式，使用file提供的方法
        //file.transferTo(new File(path, System.currentTimeMillis()+ file.getOriginalFilename()));

        // 文件路径之所以这样设置，是因为在idea部署项目时，文件保存到项目的target文件夹下，要通过部署的虚拟项目名+文件的路径访问
        // 绝对路径
        // model.addAttribute("filepath", request.getContextPath() + "/resources/files/" + filename);
        // 相对路径
        model.addAttribute("filepath", "resources/files/" + filename);
        // model.addAttribute("filepath", "/sm/resources/files/" + filename);
        return "success";
    }
    return "error";
}
```

通过注释我们可以发现有以下几点需要注意。

- 在本地运行时，文件上传的路径(path)为`本工程所在目录/target/工程名/resources/files`，其中/resources/files为代码中自己设置，如不设置则保存在target目录下的工程名所在目录。
- 使用idea部署tomcat时，选用的是`war exploded`，这是上述为什么文件保存在target目录的原因，那么当我们需要在页面上引用上传的文件时，需要使用怎样的路径呢？--->即`/虚拟项目名/resources/files/文件名`，即代码中model添加的filepath部分。

### 运行截图

- 初始页面，即项目运行时的初始页面，从此页面跳转到文件上传页面

![](https://raw.githubusercontent.com/zsy0216/image/master/notes/20191207182650.png)

- 上传文件页面，在此页面可以选择文件，并提交到服务器

![](https://raw.githubusercontent.com/zsy0216/image/master/notes/20191207182801.png)

![](https://raw.githubusercontent.com/zsy0216/image/master/notes/20191207183041.png)

- 上传成功页面，此页面如代码中所示，显示图片的访问路径和图片

![](https://raw.githubusercontent.com/zsy0216/image/master/notes/20191207183211.png)

