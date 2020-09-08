## 创建与合并分支

首先，我们创建一个分支，然后切换到该分支：

```shell
git checkout -b develop
```

- git checkout 命令加上 -b 参数表示创建并切换，相当于`git branch devolop` 和 `git checkout develop` 

- git branch 命令可以用来查看当前分支，该命令会列出所有分支，当前分支前面会标记一个 * 号

然后，我们可以在新建 dev 分支上做出修改，并进行提交。

```shell
git add README.md
git commit -m "here is something to describe."
```

现在，dev 分支上的工作已经完成，我们就可以切换到 master 分支：

```shell
# 切换到主分支
git checkout master
```

切换回 master 分支后，再查看 README.md 文件，会发现并没有内容修改，这是因为那个提交在 dev 分支，而 master 分支此刻的提交点并没有变。

现在，我们把 dev 分支的工作成果合并到 master 分支上。

```shell
# 合并 dev 分支到当前(master)分支
git merge develop
```

`git merge`命令用于合并指定分支到当前分支。合并后，再查看`readme.txt`的内容，就可以看到，和`dev`分支的最新提交是完全一样的。

在分支上提交代码

```shell
# dev 分支
git push --set-upstream origin develop
# master 分支
git push origin master
```



