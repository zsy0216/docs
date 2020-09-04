# Windows 下 git 提交脚本

每次向版本库提交代码的时候都要输入重复的命令，无形中也浪费时间。

git add .    git commit -m ""     git push ......

不如直接来一个 push.bat 脚本省时省力。

**请在第二行换成你的本地工作目录**

```bash
echo "Move to working directory"
cd E:\docs

echo "Start submitting code to the local repository"
echo "The current directory is：%cd%"
git add *
echo;
 
echo "Commit the changes to the local repository"
set now=%date% %time%
echo %now%
git commit -m "%now%"
echo;
 
echo "Commit the changes to the remote git server"
git push
echo;
 
echo "Batch execution complete!"
echo;
```

需要提交时直接双击运行即可。

或者设置为 Windows 定时任务，定时自动执行。