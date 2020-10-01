# 在 IDEA 学习 Java 源码

参考 bilibili CodeSheep

## 创建基础工程

1. 创建一个简单 Java 工程。

2. 构建基础包结构 com.zsy

3. 构建存放源码的包：source

4. 构建存放测试类的包：test

5. 从 JDK 根目录找到 `src.zip` 解压得到 Java 源码

6. 将解压得到的 Java 源码复制到工程下的 com.zsy.source 目录下

   > 注意点：复制 Java 源码到工程目录时尽量避免在 Idea 中复制，避免卡死。
   >
   > 建议：删除 com 包和 launcher 包，避免编译报错。

## 可能遇到的问题

1. 编译代码提示 ： 系统资源不足

   `Setting -> Builder, Execution,.. -> Compiler`

   设置 `Builder process heap size` 默认 `700` 为 `1700` 

2. debug 自动跳过 Java 源码 class 无法达到学习源码的目的

   `Setting -> Builder, Execution,.. -> Debugger -> Stepping` 

   取消勾选 `Do not step into the classes` 

3. debug 时跳转到的 Java 源码文件为只读，无法进行编辑注释

   `Project Structure -> Platform Settings -> SDKs -> Sourcepath` 

   `+当前工程 com.zsy.source 包 `

   `-%JAVA_HOME%/src.zip` 