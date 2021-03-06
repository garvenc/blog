本文更新于2021-05-25，使用git 2.19.0，操作系统为Windows 10。

官方中文文档：[https://git-scm.com/book/zh/v2](https://git-scm.com/book/zh/v2)。

**说明：下文中，大写为自定义变量，根据实际情况填写（个别大写的参数除外）。使用`[]`引起表示内容可选，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

[TOC]

# 运行机制

```
Working Directory      Staging Area    .git Repository HEAD
        |                    |                  |
        |       checkout the project            |
        | <------------------------------------ |
        |    stage files     |                  |
        | -----------------> |                  |
        |                    |      commit      |
        |                    | ---------------> |
```

工作目录（Working Directory）下的每一个文件都处于两种状态之一：

* 已跟踪：被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后，它们的状态可能处于未修改、已修改、已暂存、已提交。
* 未跟踪：除已跟踪文件以外的所有其它文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有放入暂存区（Staging Area）。

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
* 使用glob模式匹配的文件或目录，均忽略跟踪。匹配的为工作目录中的相对路径，可以/开头或以/结尾。
	* *匹配零个或多个任意字符。
	* [abc]匹配列表中任意一个字符。
	* ?匹配任意一个字符。
	* [a-z]匹配范围内任意一个字符。
	* **匹配任意中间目录。
* 在模式前加!，可强制跟踪该模式，即使该模式被其他模式指定为忽略跟踪。但如已忽略跟踪该模式的父目录，则使用!也不能强制跟踪。

# 选择指定的提交

命令行中可以使用如下方式，选择指定的提交：

* 使用40个字符的完整哈希值（记为HASH）。
* 使用哈希值的前缀，最少4个字符。
* 使用分支名（记为BRANCH），选择分支最顶端的提交。
* 使用BRANCH@{N}，BRANCH为分支名，N为整数，选择分支前N次的提交，N为0表示分支最顶端的提交。
* 使用\^选择父提交，可连续使用多次选择祖先提交。
* 使用\^N选择指定的父提交，只用于合并的提交，此时第一个父提交为合并时所在的分支，第二个父提交为被合入的分支。
* 使用\~选择父提交，可连续使用多次选择祖先提交，也可使用\~N简写表示连续N个\~。

# 上层命令

常用流程：

1. git clone 克隆远程仓库。
1. git config 配置。
1. git status 查看文件状态。
1. git diff 查看差异。
1. git add 暂存。
1. git reset 重置暂存区或工作目录。
1. git commit 提交。
1. git log 查看提交记录。
1. git branch 创建分支。
1. git checkout 切换分支。
1. git merge 合并分支。
1. git mergetool 解决冲突。
1. git fetch 从远程仓库拉取。
1. git push 推送至远程仓库。
1. git tag 创建标签。

## git add

将文件放入暂存区。可以跟踪新文件，暂存已修改文件，合并时将冲突标记为已解决。如指定目录，则暂存目录下的所有文件：

```shell
git add FILE
```

进入交互式暂存：

```shell
git add -i|--interactive
```

交互式暂存可输入以下子命令：

* 1或s：status，查看暂存状态。
* 2或u：update，暂存文件。
* 3或r：revert，撤销暂存文件。
* 4或a：add untracked，添加未跟踪的文件。
* 5或p：patch：暂存文件的特定部分。
* 6或d：diff，查看已暂存文件的差异。
* 7或q：quit，退出交互式暂存。
* 8或h：help，查看帮助。

输入子命令后，会列出带标号的文件列表。可使用“?”查看帮助，可以如下选择文件：

* 标号：选中单个文件，如：1。
* 标号1-标号2：选中文件范围，如：3-5。
* ,分隔的标号或标号区间：选中多个文件范围，如：2-3,6-9。
* 文件名前缀：选中带有此前缀的文件。
* -开头：取消选中。
* *：选中所有文件。
* 回车：结束并确认选择。

输入5或p，选择文件后，可输入以下子命令：

* y：暂存此块文件内容。
* n：不暂存此块文件内容。
* a：暂存此块及本文件中所有后续块的文件内容。
* d：不暂存此块及本文件中所有后续块的文件内容。
* g：选择跳至一个块。
* /：选择匹配给定正则表达式的块。
* j：不决定本块是否暂存，离开并跳至下一个未决定的块。
* J：不决定本块是否暂存，离开并跳至下一个块。
* k：不决定本块是否暂存，离开并跳至上一个未决定的块。
* K：不决定本块是否暂存，离开并跳至上一个块。
* s：将当前块分割成更小的块。
* e：手工编辑当前块。
* ?：查看帮助。

直接进入交互式部分暂存：

```shell
git add -p|--patch
```

## git bisect

二分查找出有问题的提交。

## git blame

查看文件标注，可查看每一行分别来自那次提交：

```shell
git blame [-C] [-L STARTLINE,ENDLINE] FILENAME
```

每行的结果包括以下内容：

1. 最后一次修改的提交哈希，以\^开头表示从未修改。
1. 原始文件名，当使用-C时出现，表示行内容从此文件复制过来。
1. 提交者。
1. 提交时间。
1. 文件行内容。

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
git branch BRANCH [HASH]
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

## git bundle

打包提交。

## git checkout

使用暂存区文件撤销工作目录文件的修改：

```shell
git checkout -- FILE[ ...]
```

切换工作目录至指定的标签或提交：

```shell
git checkout TAG|HASH
```

切换分支：

```shell
git checkout BRANCH
```

创建并切换分支：

```shell
git checkout -b BRANCH
```

在指定的标签上创建分支：

```shell
git checkout -b BRANCH TAG
```

交互式检出文件：

```shell
git checkout --patch BRANCH|TAG|HASH
```

建立远程分支的跟踪分支。跟踪分支的起点为远程分支：

```shell
git checkout -b BRANCH REMOTE/BRANCH
git checkout --track REMOTE/BRANCH
```

解决冲突后检出文件：

```shell
git checkout --conflict=merge|diff3 FILENAME
```

解决冲突时检出当前分支原来的文件：

```shell
git checkout --ours FILENAME
```

解决冲突时检出待合入分支的文件：

```shell
git checkout --theirs FILENAME
```

## git clean

清理工作目录，移除未被追踪的文件：

```shell
git clean
```

强制清理：

```shell
git clean -f
```

同时移除未跟踪的目录：

```shell
git clean -d
```

同时移除忽略跟踪的文件：

```shell
git clean -x
```

查看清理将移除的文件，但不实际移除：

```shell
git clean -n|--dry-run
```

交互式清理：

```shell
git clean -i
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
git config [--global] credential.helper cache|store|osxkeychain
git config --system --unset credential.helper
git config [--global] gui.encoding ENCODING
git config [--global] merge.conflictstyle merge|diff3
git config [--global] merge.tool MERGETOOL
git config [--global] pull.rebase true
git config [--global] user.email EMAIL
git config [--global] user.name NAME
```

* alias：如果SOURCE是外部命令，则需在前面加!。
* core.autocrlf：为true则checkout时转换为CRLF，commit时转换为LF；为input则checkout时不做转换，commit时转换为LF；为false则checkout和commit时都不做转换。
* core.bare：是否设为裸仓库。裸仓库可作为远程仓库往其push。
* core.editor：默认编辑器。
* core.quotepath：是否将文件路径中0x80以上的字符转义为八进制。
* credential.helper：凭证存储方式。cache为保存在内存中，可附加参数--timeout SECONDS；store为明文保存在磁盘中，可附加参数--file FILENAME；osxkeychain在Mac下使用，密文保存在磁盘中。如需清除之前保持的凭证（例如：提示“You are not allowed to push code to this project.”），则使用--system --unset。
* gui.encoding：GUI中的字符编码，如：utf-8。
* merge.conflictstyle：合并冲突时冲突的样式。merge有ours、theirs的数据，diff3有ours、theirs、base的数据。
* merge.tool：合并冲突的工具，如：kdiff3。需同时指定mergetool.MERGETOOL.path。
* mergetool.MERGETOOL.path：合并冲突的工具的可执行文件路径。MERGETOOL需与merge.tool指定的值相同。
* user.email：作者邮箱。
* user.name：作者名字。

删除配置：

```shell
git config --unset KEY
```

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

## git credential

凭证辅助工具。

## git diff

比较工作目录和暂存区的差异，使用-b在比较时忽略空白符的差异：

```shell
git diff [-b]
```

比较暂存区和已提交的差异：

```shell
git diff --staged|--cached
```

比较合并的结果与当前分支原来的差异：

```shell
git diff --ours
```

比较合并的结果与待合入分支的差异：

```shell
git diff --theirs
```

三路比较合并差异：

```shell
git diff --base
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

## git gc

清理垃圾文件。

```shell
git gc
```

## git grep

搜索文件内容，可搜索工作目录和所有提交：

```shell
git grep [OPTIONS] REGEXP
```

OPTIONS可使用如下选项：

* -c或--count：只显示匹配的计数概要。
* -n或--line-number：显示匹配的行号。
* -p或--show-function：显示匹配的行所在的函数。
* --and：同一行同时匹配多个值。
* --break：在不同文件的匹配之间显示空行。
* --heading：在同一个文件的匹配之前显示文件名。

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

查看分支的提交历史：

```shell
git log [OPTIONS] [BRANCH ...]
```

双点（..为命令行输入的字符）查看提交区间，查看在BRANCH2中而不在BRANCH1中的提交历史：

```shell
git log [OPTIONS] BRANCH1..BRANCH2
```

三点（...为命令行输入的字符）查看提交区间，查看被两个分支之一包含，但不被同时包含的提交：

```shell
git log [OPTIONS] [--left-right] BRANCH1...BRANCH2
```

--left-right使用“<”和“>”表示提交属于左侧的分支还是右侧的分支。

多点查看提交区间，使用^或--not指定提交不在其中的分支：

```shell
git log [OPTIONS] BRANCH1 [ ...] [^BRANCH2 [ ...]] [--not BRANCH3 [ ...]]
```

OPTIONS可使用如下选项：

* 基本选项：
	* -- PATH：放在最后位置上，指定文件路径。
	* -L :FUNCNAME:FILENAME或-L '/REGEXP/':FILENAME：行日志搜索，查看指定函数的提交历史。
	* --decorate：查看各个分支当前所指的对象。
* 提交范围选项：
	* -(n)：最近n次提交。
	* -g：显示引用日志，即类似git reflog。
	* --after TIME：显示指定时间之后的提交，同--since。
	* --all：显示所有分支的提交信息。
	* --all-match：显示同时满足这所有选项搜索条件的提交，不带此选项则显示满足任意一个搜索条件的提交。
	* --author AUTHOR：显示指定作者相关的提交。
	* --before TIME：显示指定时间之前的提交，同--until。
	* --committer COMMITTER：显示指定提交者相关的提交。
	* --grep WORD：显示提交说明（即git commit -m指定的内容）中含指定关键字的提交。
	* --since TIME：显示指定时间之后的提交，同--after。
	* --until TIME：显示指定时间之前的提交，同--before。
* 提交排序选项：
	* --date-order：按提交时间排序。
* 格式化选项：
	* --abbrev-commit：仅显示SHA-1的前几个字符，而非所有的40个字符。
	* --graph：使用ASCII图表展示分支，需与--pretty=oneline|format结合使用。
	* --oneline：每次提交信息单行显示，类似--pretty=oneline。
	* --pretty=oneline|short|full|fuller|format:"FORMAT"：使用指定格式显示。
	* --relative-date：使用较短的相对时间显示（比如，“2 weeks ago”）。
* 差异比较选项：
	* -G REGEXP：显示提交文件内容中匹配正则表达式的提交。
	* -p：显示每次提交的内容差异。
	* -S WORD：显示提交文件内容中含指定关键字的提交。
	* --name-only：仅在提交信息后显示已修改的文件清单。
	* --name-status：显示新增、修改、删除的文件清单。
	* --shortstat：只显示--stat中最后的行数修改添加移除统计。
	* --stat：显示每次提交的简略的统计信息。

常用命令：

* 查看分支历史图表：git log --oneline --graph --all

## git ls-remote

查看并更新远程仓库的所有引用：

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

将没有共同祖先提交的不关联分支合并到当前分支：

```shell
git merge --allow-unrelated-histories BRANCH|HASH
```

合并时忽略空白：

```shell
git merge -Xignore-all-space|-Xignore-space-change BRANCH|HASH
```

* -Xignore-all-space： 完全忽略空白符的修改。
* -Xignore-space-change：将一个空白符与多个连续的空白字符视作等价的。

合并时冲突使用当前分支的内容：

```shell
git merge -Xours BRANCH|HASH
```

合并时冲突使用待合入分支的内容：

```shell
git merge -Xtheirs BRANCH|HASH
```

中断合并：

```shell
git merge --abort
```

如有合并冲突，需使用`git mergetool`解决。

## git merge-file

合并文件，如使用-p则将结果打印而不是写入CURRENTFILENAME：

```shell
git merge-file [-p] CURRENTFILENAME BASEFILENAME OTHERFILENAME
```

使用当前分支的内容合并文件：

```shell
git merge-file --ours FILENAME
```

使用待合入分支的内容合并文件：

```shell
git merge-file --theirs FILENAME
```

## git mergetool

可视化合并冲突解决：

```shell
git mergetool [FILENAME]
```

使用kdiff3合并：A(Base)为共同祖先；B(Local)为当前分支；C(Remote)为待合入的分支；Ouput为合并结果。合并成功后，会保存后缀为.orig的冲突文件，其中描述的冲突内容如下：

1. "<<<<<<<"表示本地分支，其后跟随本地分支名。
1. 本地分支内容。
1. "======="分隔本地分支和待合入的分支。
1. 待合入分支内容。
1. ">>>>>>>"表示待合入分支，其后跟随待合入分支名。

合并冲突解决后，需使用`git commit`提交。

kdiff3可通过设置修改字符编码：“Settings -> Configure KDiff3... -> Regional Settings”，选择“File Encoding for”开头的对应下拉框为“Unicode, 8 bit (UTF-8)”。

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
git push -u|--set-upstream REMOTE REMOTEBRANCH
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

变基。将当前分支指向目标基底分支BRANCH，并应用当前分支相较于最近共同祖先的修改。一般这样做的目的是为了向远程分支推送时保持提交历史的整洁：

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

交互式变基，将指定提交及之后的提交重写：

```shell
git rebase -i HASH
```

其列出的提交历史是从旧至新的，修改提交历史前的命令，退出后`git commit --amend`修改`edit`命令指定的提交，`git rebase --continue`自动应用剩余的提交。

## git reflog

查看引用日志，引用日志只保存在本地仓库，且只会保存一段时间：

```shell
git reflog
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

## git replace

替换提交。

## git rerere

重用记录的解决方案（reuse recorded resolution）。

## git reset

注意，此处的HEAD是HEAD指针而不是自定义变量。

COMMIT缺省或为HEAD则恢复到上一次提交；为HEAD^则恢复到倒数第二次提交，即丢弃上一次提交；为HEAD~N则恢复到倒数第N+1次提交，即丢弃最近N次提交。

恢复暂存区，但不改变工作目录，相当于回滚`git add`：

```shell
git reset [FILENAME]
```

将HEAD恢复至指定的提交，但不改变暂存区和工作目录，相当于回滚`git commit`：

```shell
git reset --soft [COMMIT] [FILENAME]
```

将HEAD和暂存区恢复至指定的提交，但不改变工作目录，相当于回滚`git add`和`git commit`：

```shell
git reset [--mixed] [COMMIT] [FILENAME]
```

将HEAD、暂存区和工作目录恢复到指定的提交，如工作目录有未提交的内容则会丢失：

```shell
git reset --hard [COMMIT] [FILENAME]
```

交互式恢复：

```shell
git reset --patch
```

## git rev-parse

解析提交的完整哈希：

```shell
git rev-parse BRANCH
```

## git revert

还原至合并前：

```shell
git revert [-m N] HEAD
```

N的值：1为还原至本地分支，2为还原至待合入分支。

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

查看合并冲突的文件内容：

```shell
git show :N:FILENAME
```

N的值：1为共同祖先；2为当前分支；3为待合入的分支。

## git stash

储藏改动：

```shell
git stash
git stash push [-m MSG]
```

还可以同时使用以下参数：

* --all或-a：同时储藏未跟踪的文件和忽略跟踪的文件。
* --include-untracked或-u：同时贮藏未跟踪的文件，但不包括忽略跟踪的文件。
* --keep-index：不仅贮藏已暂存的文件，还将它们保留在索引中。
* --patch：交互式储藏。

查看储藏栈，每条以stash@{N}标记，stash@{0}为栈顶，即最近的储藏：

```shell
git stash list
```

应用储藏，如不指定储藏标记，则默认应用最近的储藏。可以在一个不干净的工作目录，或其它分支上应用储藏，但可能产生合并冲突：

```shell
git stash apply [--index] [stash@{N}]
```

使用--index可同时应用暂存，否则只应用文件的改动，不会重新暂存。

移除储藏：

```shell
git stash drop stash@{N}
```

应用栈顶的储藏后移除该储藏。可以在一个不干净的工作目录，或其它分支上应用储藏，但可能产生合并冲突：

```shell
git stash pop [--index]
```

从储藏创建分支：

```shell
git stash branch BRANCH [stash@{N}]
```

## git status

查看当前文件状态。path可使用glob模式匹配：

```shell
git status [path]
```

默认只输出未跟踪文件的目录，如需同时输出未跟踪文件：

```shell
git status -u [path]
```

查看简短的输出：

```shell
git status [-s|--short] [path]
```

输出含义如下：

* ??：未跟踪。
* A：新跟踪到暂存区。
* 右M：已修改但未放入暂存区。
* M左：已修改并放入暂存区。

## git submodule

使用子模块。

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
