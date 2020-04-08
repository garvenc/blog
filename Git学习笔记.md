本文更新于2020-04-07，使用git 2.19.0，操作系统为Windows 10。

官方中文文档：[https://git-scm.com/book/zh/v2](https://git-scm.com/book/zh/v2)。

**说明：下文中，大写为自定义变量，根据实际情况填写（个别大写的参数除外）。使用`[]`引起表示内容可选，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

[TOC]

# 基本概念

git文件可能处于三种状态之一：已修改（modified）、已暂存（staged）和已提交（committed）。

工作目录下的每一个文件都处于两种状态之一：已跟踪或未跟踪。 已跟踪是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后，它们的状态可能处于未修改，已修改或已暂存。 工作目录中除已跟踪文件以外的所有其它文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有放入暂存区。 

下文中的.目录均为工作目录，命令均在工作目录下执行。

# 配置文件

Linux配置文件读取顺序：

1. /etc/gitconfig
1. \~/.gitconfig或\~/.config/git/config
1. .git/config

Windows配置文件读取顺序：

1. C:/Users/$USER/.gitconfig
1. .git/config

# .gitignore

配置忽略跟踪的文件列表。

* 以#开头的行是注释。
* 使用glob模式匹配的文件，均忽略跟踪。
	* *匹配零个或多个任意字符。
	* [abc]匹配列表中任意一个字符。
	* ?匹配任意一个字符。
	* [a-z]匹配范围内任意一个字符。
	* **匹配任意中间目录。
* 以/开头指定在工作目录中的相对路径，并忽略跟踪。
* 以/结尾指定目录，并忽略跟踪。
* 在模式前加!，可强制跟踪该模式，即使该模式被其他模式指定为忽略跟踪。但如已忽略跟踪该模式的父目录，则使用!也不能强制跟踪。

# 命令

## git add

将文件放入暂存区。可以跟踪新文件，暂存已修改文件，合并时将冲突标记为已解决。如指定目录，则暂存目录下的所有文件：

```shell
git add FILE
```

## git branch

查看所有分支。使用-v参数可查看每一个分支的最后一次提交。使用-vv参数可查看所有跟踪分支：

```shell
git branch
git branch -v
git branch -vv
```

查看已合并到当前分支的分支：

```shell
git branch --merged
```

查看未合并到当前分支的分支：

```shell
git branch --no-merged
```

创建分支。不会切换到新分支：

```shell
git branch BRANCH
```

删除分支：

```shell
git branch -d BRANCH
```

强制删除分支。如果分支包含未合并的提交，则会将其丢弃：

```shell
git branch -D BRANCH
```

设置当前的本地分支跟踪拉取下来的远程分支，或者修改正在跟踪的上游分支。当设置好跟踪分支后，可以通过`@{upstream}`或`@{u}`快捷方式来引用它：

```shell
git branch -u REMOTE/BRANCH
git branch --set-upstream-to REMOTE/BRANCH
```

## git checkout

使用暂存区文件撤销工作目录文件的修改：

```shell
git checkout -- FILE...
```

切换分支：

```shell
git checkout BRANCH
```

切换工作目录至指定的标签或提交：

```shell
git checkout TAG|HASH
```

创建并切换分支：

```shell
git checkout -b BRANCH
```

在指定的标签上创建分支：

```shell
git checkout -b BRANCH TAG
```

建立远程分支的跟踪分支。跟踪分支的起点为远程分支：

```shell
git checkout -b BRANCH REMOTE/BRANCH
git checkout --track REMOTE/BRANCH
```

## git clone

克隆仓库。如没有指定目标目录，则会在当前目录下以源路径最后一个目录名为目标目录。目标目录必须为空目录：

```shell
git clone SRC [DST]
```

## git config

查看配置：

```shell
git config --list
git config KEY
```

设置配置：

```shell
git config [--global] alias.ALIAS SOURCE
git config [--global] core.autocrlf true|input|false
git config --bool core.bare true
git config [--global] core.editor EDITOR
git config [--global] core.quotepath false
git config [--global] gui.encoding ENCODING
git config [--global] pull.rebase true
git config [--global] user.email EMAIL
git config [--global] user.name NAME
```

* alias：如果SOURCE是外部命令，则需在前面加!。
* core.autocrlf：为true则checkout时转换为CRLF，commit时转换为LF；为input则checkout时不做转换，commit时转换为LF；为false则checkout和commit时都不做转换。
* core.bare：是否设为裸仓库。裸仓库可作为远程仓库往其push。
* core.editor：默认编辑器。
* core.quotepath：是否将文件路径中0x80以上的字符转义为八进制。
* gui.encoding：GUI中的字符编码，如：utf-8。
* user.email：作者邮箱。
* user.name：作者名字。

## git commit

将暂存区提交：

```shell
git commit [-m MSG]
```

将已跟踪过的文件暂存并提交：

```shell
git commit -a [-m MSG]
```

撤销上一次提交，重新提交：

```shell
git commit --amend
```

## git diff

比较工作目录和暂存区的差异：

```shell
git diff
```

比较暂存区和已提交的差异：

```shell
git diff --staged|--cached
```

## git fetch

从远程仓库拉取。不会自动合并分支：

```shell
git fetch [REMOTE]
```

拉取所有远程仓库：

```shell
git fetch --all
```

## git gui

打开图形用户界面：

```shell
git gui
```

## git help

查看帮助：

```shell
git help VERB
git BERB --help
man git-VERB
```

## git init

在当前目录初始化仓库。目录中可以存在文件。远程仓库最好使用--bare参数初始化，且之后仓库不能作为工作目录：

```shell
git init [--bare]
```

## git log

查看提交历史：

```shell
git log [BRANCH ...]
```

可使用如下选项：

* -(n)：最近n次提交。
* -p：显示每次提交的内容差异。
* -S WORD：显示提交内容中含指定关键字的提交。
* --abbrev-commit：仅显示SHA-1的前几个字符，而非所有的40个字符。
* --after TIME：显示指定时间之后的提交，同--since。
* --all：显示所有分支的提交信息。
* --all-match：显示同时满足这所有选项搜索条件的提交，不带此选项则显示满足任意一个搜索条件的提交。
* --author AUTHOR：显示指定作者相关的提交。
* --before TIME：显示指定时间之前的提交，同--until。
* --committer COMMITTER：显示指定提交者相关的提交。
* --decorate：查看各个分支当前所指的对象。
* --graph：使用ASCII图表展示分支，需与--pretty=oneline|format结合使用。
* --grep WORD：显示提交说明中含指定关键字的提交。
* --name-only：仅在提交信息后显示已修改的文件清单。
* --name-status：显示新增、修改、删除的文件清单。
* --oneline：每次提交信息单行显示，类似--pretty=oneline。
* --pretty=oneline|short|full|fuller|format:"FORMAT"：使用指定格式显示。
* --relative-date：使用较短的相对时间显示（比如，“2 weeks ago”）。
* --shortstat：只显示--stat中最后的行数修改添加移除统计。
* --since TIME：显示指定时间之后的提交，同--after。
* --stat：显示每次提交的简略的统计信息。
* --until TIME：显示指定时间之前的提交，同--before。
* -- PATH：放在最后位置上，指定文件路径。

常用命令：

* 查看分支历史图表：git log --oneline --graph --all

## git ls-remote

查看远程引用完整列表：

```shell
git ls-remote [REMOTE]
```

## git merge

合并分支。将分支合并到当前分支：

```shell
git merge BRANCH|HASH
```

合并分支。将分支合并到当前分支，总是创建一个提交而不使用快进（fast-forward）：

```shell
git merge --no-ff BRANCH|HASH
```

中断合并：

```shell
git merge --abort
```

如有合并冲突，需使用`git mergetool`解决。

## git mergetool

可视化合并冲突解决：

```shell
git mergetool
```

使用kdiff3合并：A(Base)为共同祖先；B(Local)为当前分支；C(Remote)为待合入的分支；Ouput为合并结果。合并成功后，会保存后缀为.orig的冲突文件，其中描述的冲突内容如下：

1. "<<<<<<<"表示本地分支，其后跟随本地分支名。
1. 本地分支内容。
1. "======="分隔本地分支和待合入的分支。
1. 待合入分支内容。
1. ">>>>>>>"表示待合入分支，其后跟随待合入分支名。

合并冲突解决后，需使用`git commit`提交。

kdiff3可通过设置修改字符编码：Settings -> Configure KDiff3... -> Regional Settings，选择File Encoding for开头的对应下拉框为Unicode, 8 bit (UTF-8)。

## git mv

移动文件：

```shell
git mv FROM TO
```

## git pull

从远程仓库拉取。会自动合并到跟踪分支。通常单独显式地使用fetch与merge命令会更好一些：

```shell
git pull [--rebase] [REMOTE [BRANCH]]
git pull --allow-unrelated-histories [REMOTE [BRANCH]]
```

当本地仓库和远程仓库是两个独立的仓库时，会出现错误：fatal: refusing to merge unrelated histories，此时需加上参数`--allow-unrelated-histories`。

## git push

推送到远程仓库。并不会推送标签。如使用--force或-f，则强制推送，可能会导致远程仓库已有的提交丢失：

```shell
git push [REMOTE] [[LOCALBRANCH|HASH:]REMOTEBRANCH] [--force|-f]
```

如出现如下错误，则在远程仓库执行`git config --bool core.bare true`。

```
remote: error: refusing to update checked out branch: refs/heads/master
remote: error: By default, updating the current branch in a non-bare repository
```

推送的时候同时在远程仓库创建分支：

```shell
git push --set-upstream REMOTE REMOTEBRANCH
```

删除远程分支：

```shell
git push REMOTE --delete REMOTEBRANCH
```

推送标签到远程仓库：

```shell
git push REMOTE TAG
```

推送所有为推送的标签到远程仓库：

```shell
git push REMOTE --tags
```

删除远程仓库的标签：

```shell
git push REMOTE :refs/tags/TAG
```

## git rebase

**不要对在你的仓库外有副本的分支执行变基。**变基操作的实质是丢弃一些现有的提交，然后相应地新建一些内容一样但实际上不同的提交。

变基。将当前分支的指针指向目标基底分支，并应用当前分支相较于最近共同祖先的修改。一般这样做的目的是为了向远程分支推送时保持提交历史的整洁：

```shell
git rebase BRANCH
```

将特性分支TOPICBRANCH变基到目标基底分支BASEBRANCH：

```shell
git rebase BASEBRANCH TOPICBRANCH
```

取出BRANCH3分支，找出BRANCH3和BRANCH2共同祖先之后的修改，然后在BRANCH1分支上重放：

```shell
git rebase --onto BRANCH1 BRANCH2 BRANCH3
```

## git remote

查看本地仓库的远程仓库信息：

```shell
git remote [-v]
git remote show REMOTE
```

添加远程仓库：

```shell
git remote add SHORTNAME URL
```

重命名远程仓库：

```shell
git remote rename OLDNAME NEWNAME
```

移除远程仓库：

```shell
git remote rm REMOTE
```

## git reset

注意，此处的HEAD不是自定义变量。

从暂存区移除跟踪文件，回到已修改状态：

```shell
git reset [HEAD] [FILE[ ...]]
```

将暂存区和工作目录恢复到上一次提交：

```shell
git reset --hard [HEAD]
```

将暂存区和工作目录恢复到倒数第二次提交，即丢弃上一次提交：

```shell
git reset --hard HEAD^
```

将暂存区和工作目录恢复到倒数第N+1次提交，即丢弃最近N次提交：

```shell
git reset --hard HEAD~N
```

## git rm

从暂存区移除跟踪文件，并从工作目录删除文件。若文件已修改且已放入暂存区，则需要使用-f：

```shell
git rm [-f] FILE
```

只从暂存区移除跟踪文件，不从工作目录删除文件：

```shell
git rm --cached FILE
```

## git show

查看某次提交的信息：

```shell
git show [--stat] HASH
```

* --stat：显示每次提交的简略的统计信息。

## git status

查看当前文件状态。使用-s或--short可查看简短的输出。path可使用glob模式匹配：

```shell
git status [-s|--short] [path]
```

输出含义如下：

* ??：未跟踪。
* A：新跟踪到暂存区。
* 右M：已修改但未放入暂存区。
* M左：已修改并放入暂存区。

## git tag

查看已有的标签，包括附注标签和轻量标签。以字母顺序列出：

```shell
git tag [-l GLOB]
```

创建附注标签（annotated）。指定校验和或校验和前缀，可对指定的提交创建标签：

```shell
git tag -a TAG -m MSG [HASH]
```

创建轻量标签（lightweight）。指定校验和或校验和前缀，可对指定的提交创建标签：

```shell
git tag TAG [HASH]
```

删除标签：

```shell
git tag -d TAG
```

# 常用流程

1. git clone 克隆远程仓库。
1. git config 配置。
1. git branch 创建分支。
1. git checkout 切换分支。
1. git merge 合并分支。
1. git mergetool 解决冲突。
1. git add 暂存。
1. git commit 提交。
1. git remote 添加远程仓库。
1. git pull 从远程仓库拉取。
1. git push 推送至远程仓库。
