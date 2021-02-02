> IDEA 配置记录，记录那些重要而又记不住的配置

## 设置默认字符编码为 UTF-8

`Setting -> Editor -> File Encodings`

Transparent native-to-ascii conversion 勾选避免 Properties 等文件中的注释显示乱码

![image-20201121115651654](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20201121115651654.png)

## 新建 java 类自动生成注释

`Setting -> Editor -> File and Code Templates -> Files -> Class`

```java
/**
 * @author: zsy
 * @date: ${DATE} ${TIME}
 */
```

![image-20201121103658717](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20201121103658717.png)

## IDEA 隐藏不需要的文件

`Setting -> Editor -> File Types` 

![image-20201121110417297](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20201121110417297.png)

## 在工具栏增加 git push 按钮

`File--->Settings--->Appearance & Behavior ---> Menus and ToolBars--->Navigation Bar Toolbar--->NavBarVcsGroup--->VcsNavBarToobarActions` 

![image-20201204220425005](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20201204220425005.png)

点击 + 号，选择 `Add Action -> version Control Systems--->Git--->Repository` 选择push按钮，然后调整出现的位置即可

## 设置默认换行符

`Setting -> Editor -> Code Style` 

选择 Line separator 为想要的系统换行符

![image-20201125212402096](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20201125212402096.png)

## 设置注释起始位置不在第一行

`Setting -> Editor -> Code Style -> Java -> Code Generation -> Comment Code` 

取消勾选 Line comment at first column

*可能误操作 Override Method Signature --- Repeat synchronized modifer* 

![image-20201217211256498](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20201217211256498.png)

## 设置Lombok Annotation Processors 注解处理器

`Setting -> Build,Execution,Deployment -> Compiler -> Annotation Processors` 

勾选 Enable annotation processing

![image-20210202095514205](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20210202095514205.png)