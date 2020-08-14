# 1.docker安装

```shell
#1.检查内核版本，必须是3.10及以上
uname -r
#2.安装
yum -y install docker 
```

# 2.docker简单使用

```shell
#1.启动docker
systemctl start docker
#1.1.验证
docker -v
#2.设置开机自启
systemctl enable docker
#2.1.关闭开机自启
systemctl disable docker
#3.停止docker
systemctl stop docker
```

# 3.Docker常用命令&操作

## 1）、镜像操作

| 操作 | 命令                                            | 说明                                                     |
| ---- | ----------------------------------------------- | -------------------------------------------------------- |
| 检索 | docker  search 关键字  eg：docker  search redis | 我们经常去docker  hub上检索镜像的详细信息，如镜像的TAG。 |
| 拉取 | docker pull 镜像名:tag                          | :tag是可选的，tag表示标签，多为软件的版本，默认是latest  |
| 列表 | docker images                                   | 查看所有本地镜像                                         |
| 删除 | docker rmi image-id                             | 删除指定的本地镜像                                       |

https://hub.docker.com/

```shell
#1.搜索镜像
docker search imagename
docker search mysql
#2.拉取镜像
#docker pull imagename:tag #不加tag时默认拉取最新版本即:latest
docker pull mysql:5.7
#3.查看已经拉取的镜像
docker images
REPOSITORY          TAG         IMAGE ID            CREATED             SIZE
docker.io/mysql     5.7         7faa3c53e6d6        3 weeks ago         373MB 
#4.删除镜像
#docker rmi imageid/name
docker rmi 7faa3c53e6d6
docker rmi mysql:5.7
```

## 2）、容器操作

镜像一经运行，就产生了与之对应的容器。可以多次启动镜像产生不同的容器。

容器启动后会有一个唯一标识id，可根据id或启动时对容器的自定义命名进行操作。

```shell
#1.开启容器
docker run --name mysql01 -d mysql:5.7
#2.查看运行中容器
docker ps
CONTAINER ID     IMAGE       NAMES
dde9e736431d     mysql:5.7   mysql01
#3.查看所有容器（包括运行中和已关闭的）
docker ps -a
#4.停止容器运行
#docker stop container id/name
docker stop dde9e736431d
docker stop mysql01
#5.重新启动容器
#docker start container id/name
docker start dde9e736431d
docker start mysql01
#6.删除容器
#docker rm container id/name
docker rm dde9e736431d
docker rm mysql01
```

docker run 命令还有其他的参数：

```shell
-d：后台运行
-p: 端口映射
--name：自定义命名
--restart=always：设置容器随docker启动
#其他参数参考docker hub中搜索到的容器下面给出的示例.
```

若容器已经启动，可根据以下命令添加配置：

```shell
#docker update --restart=always container id
docker update --restart=always dde9e736431d
```

