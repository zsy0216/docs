## 1.磁盘管理

1. `pwd `：print working directory，显示当前工作目录

2. `ls`：list，列出目前工作目录所含之文件及子目录

   ​	`ls directory name`:列出该目录下的文件及其子目录

   ​	`ls -l`：显示文件和目录的详细信息

3. `cd`：change directory 改变目录

   ​	`cd directory name`：进入到dirName该目录下

4. `/`：表示根目录

5. `~`：表示/home/yourusername

6. `.`：表示当前目录

7. `..`：表示上一层目录

8. `-`：表示上次打开的目录

## 2.文件

1. `touch`：创建文件

   ​	`touch filename`：创建一个名字为filename的文件

2. `cat`：读取文件

   ​	`cat filename`：打印文件内容

3. `mkdir`：make directory，创建目录

   ​	`mkdir dirName`：创建一个名字为dirName的目录

4. `mv`：move，移动文件

   ​	`mv filename dirName`：移动filename文件到dirName目录

5. `rm`：remove，删除文件

   ​	`rm filename`：删除名字为filename 的文件

6. `rmdir`：remove directory，删除一个目录（空）

   ​	`rmdir dirName`：删除该目录

   ​	`rm -r dirName`：对于非空目录的删除，可以使用`rm -r `递归删除文件

7. `tar`：解压文件

   ​	`tar -zxvf filename`解压文件

8. `man`：查看使用手册

   ​	`man command`：查看该命令的使用手册，按q退出

## 3.shell快捷键

`ctrl+a`：移动到命令行最初始位置

`ctrl+e`：移动到命令行最末端位置

`ctrl+p`：查看上一条命令

`ctrl+n`：查看下一条命令

`ctrl+r`：反向查找历史记录

`ctrl+c`：中断

## 4.用户管理

1. 普通用户切换到管理员：

      `su - root`，之后输入root密码

   注：切换非root用户不需要输入密码

2. 查看自己的身份：

     `whoami` 


## 5.系统管理

查看所有进程
`ps -ef`：显示所有进程信息，连同命令行

`ps -ef | grep xxx`：显示xxx对应的所有进程