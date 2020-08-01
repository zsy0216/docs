# Commit message 的格式

每次提交，Commit message 都包括三个部分：Header，Body 和 Footer。

```shell
<type>(<scope>): <subject>// 空一行<body>// 空一行<footer>
```

> 注意 冒号后面有空格。 

其中，Header 是必需的，Body 和 Footer 可以省略。

不管是哪一个部分，任何一行都不得超过72个字符（或100个字符）。这是为了避免自动换行影响美观。

## Header

平时提交时最常用的是只有 Header，这里介绍一下 Header 的使用说明

Header 部分只有一行，包括三个字段：`type`（必需）、`scope`（可选）和`subject`（必需）。

### type

`type` 用于说明 commit 的类别，只允许使用下面7个标识。

```shell
feat：新功能（feature）
fix：修补bug
docs：文档（documentation）
style： 格式（不影响代码运行的变动）
refactor：重构（即不是新增功能，也不是修改bug的代码变动）
test：增加测试
chore：构建过程或辅助工具的变动
```

如果 `type` 为 `feat ` 和 `fix`，则该 commit 将肯定出现在 Change log 之中。其他情况（`docs`、`chore`、`style`、`refactor`、`test`）由你决定，要不要放入 Change log，建议是不要。

### scope

`scope` 用于说明 commit 影响的范围，比如 middleware(中间件) 、core(核心) 、config(配置文件) 、plugin(插件) 、test(测试)等等，视项目不同而不同。

### subject

`subject` 是 commit 目的的简短描述，不超过50个字符。

- 以动词开头，使用第一人称现在时，比如 `change`，而不是 `changed` 或 `changes`
- 第一个字母小写
- 结尾不加句号（`.`）

