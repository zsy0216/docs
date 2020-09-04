# Git 命令学习

## 基本提交命令
```shell
git add .
git commit -m "message"
git push
```

## Git 版本回退 （另一个版本）

```shell
git reset --hard HEAD^
```

或前往任意一个版本（需要知道版本对应的 commitID）

```shell
git reset --hard commitID
```

commitId 可以通过 `git reflog` 查看



## 提交后如何撤销 commit

```shell
git reset --soft HEAD^
```

HEAD^的意思是上一个版本，也可以写成HEAD~1

如果你进行了2次commit，想都撤回，可以使用HEAD~2

- 其他参数
  ```shell
	--mixed 
	意思是：不删除工作空间改动代码，撤销commit，并且撤销git add . 操作
	这个为默认参数,git reset --mixed HEAD^ 和 git reset HEAD^ 效果是一样的。
	 
	--soft  
	不删除工作空间改动代码，撤销commit，不撤销git add . 
	 
	--hard
	删除工作空间改动代码，撤销commit，撤销git add . 
	* 注意完成这个操作后，就恢复到了上一次的commit状态。
  ```
  
- 修改 commit 信息
  ```shell
	git commit --amend
  ```