# 新建分支并切换

```shell
git checkout -b develop
```

这样就可以切换到新建的分支了，我们可以在新建分支上进行开发，并进行提交

```shell
git add README.md
git commit -m "here is something to describe."
```

# 在分支上提交代码

```shell
git push --set-upstream origin develop
```

# 与主分支合并

```shell
# 查看当前分支
git branch
# 切换到主分支
git checkout master
# 合并开发分支
git merge develop
# 提交
git push origin
```

