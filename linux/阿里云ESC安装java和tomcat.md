# JDK

首先从官网上下载 jdk ----- `jdk-8u231-linux-x64.tar.gz` 到`/opt` 目录。

并移动到服务器上的 `/usr/java` 目录下；

解压：

```shell
tar -zxvf jdk-8u231-linux-x64.tar.gz
```

删除压缩包：

```shell
rm -f jdk-8u231-linux-x64.tar.gz
```

移动到指定目录

```shell
mkdir /usr/java
mv /opt/jdk1.8.0_231/ /usr/java/jdk1.8.0_231
```

**环境变量配置：** 

vim编辑：

```shell
vim /etc/profile
```

在文件最后加上：

```
# java1.8.0_231 
export JAVA_HOME=/usr/java/jdk1.8.0_231
export JRE_HOME=/usr/java/jdk1.8.0_231/jre
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
export PATH=$JAVA_HOME/bin:$PATH
```

使生效：

```shell
source /etc/profile
```

# Tomcat

首先从官网上下载 jdk ----- `apache-tomcat-8.5.51.tar.gz`

```shell
# 解压
tar -zxvf apache-tomcat-8.5.51.tar.gz 
# 删除无用的压缩包
rm -f apache-tomcat-8.5.51.tar.gz 
# 改名
mv apache-tomcat-8.5.51 tomcat8.5.51 
# 环境变量配置
vim /etc/profile

#文件最后加上：
# tomcat8.5.51
export CATALINA_HOME=/usr/java/tomcat8.5.51
export CATALINA_BASE=/usr/java/tomcat8.5.51
#export TOMCAT_HOME=/usr/java/tomcat8.5.51
export PATH=$PATH:$CATALINA_BASE/bin:$PATH

#使生效
source /etc/profile
#验证
cd /usr/java/tomcat8.5.51/bin
./startup.sh
```