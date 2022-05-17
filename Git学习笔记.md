本文更新于2022-04-21，使用git 2.19.0，操作系统为Windows 10。

官方中文文档：[https://git-scm.com/book/zh/v2](https://git-scm.com/book/zh/v2)。

**说明：下文中，大写为自定义变量，根据实际情况填写（个别大写的参数除外）。使用`[]`引起表示内容可选，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

[TOC]

# 安装

在Debian 10安装：

```shell
sudo apt-get install git
```

# 运行机制

```
Workspace    Index/Stage    Repository      Remote
   |              |             |             |
   |     add      |   commit    |    push     |
   |------------->|------------>|------------>|
   |           checkout         |    fetch    |
   |<---------------------------|<------------|
   |                   pull                   |
   |<-----------------------------------------|
```

* 工作区（Workspace）：计算机中看到的目录，持有实际文件。工作区下每个文件都处于以下两种状态之一：
	* 已跟踪：被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后，它们的状态可能处于未修改、已修改、已暂存、已提交。
	* 未跟踪：除已跟踪文件以外的所有其它文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有放入暂存区。
* 暂存区（Index/Stage）：临时保存改动。
* 版本库（Repository）：工作区目录下的.git目录。
* 远程仓库（Remote）：托管在远程计算机上的版本库，可供多人分布式开发。

下文中的.目录均为工作区，命令均在工作区下执行。

# 配置文件

Linux配置文件读取顺序：

1. /etc/gitconfig
1. \~/.gitconfig或\~/.config/git/config
1. .git/config

Windows配置文件读取顺序：

1. C:/Users/$USER/.gitconfig
1. .git/config

# .gitignore

配置忽略跟踪的文件列表。若文件已被跟踪，则规则对其无效。

* 以#开头的行是注释。
* 使用glob模式匹配的文件或目录，均忽略跟踪。以/开头表示工作区根目录，以/结尾表示目录。
	* *匹配零个或多个任意字符。
	* ?匹配任意一个字符。
	* [abc]匹配列表中任意一个字符。
	* [a-z]匹配范围内任意一个字符。
	* **匹配任意中间目录。
* 在模式前加!，可强制跟踪该模式，即使该模式被其他模式指定为忽略跟踪。但如已忽略跟踪该模式的父目录，则使用!也不能强制跟踪。

# 选择指定的提交

命令行中可以使用如下方式，选择指定的提交（记为COMMIT）：

* 使用40个字符的完整哈希值（记为HASH）。
* 使用哈希值的前缀，最少4个字符。
* 使用分支名（记为BRANCH），选择分支最顶端的提交。
* 使用BRANCH@{N}，BRANCH为分支名，N为整数，选择分支前N次的提交，N为0表示分支最顶端的提交。
* 使用\^选择父提交，可连续使用多次选择祖先提交。
* 使用\^N选择指定的父提交，只用于合并的提交，此时第一个父提交为合并时所在的分支，第二个父提交为被合入的分支。
* 使用\~选择父提交，可连续使用多次选择祖先提交，也可使用\~N简写表示连续N个\~。

# 上层命令

查看版本：

```shell
git --version
```

常用流程：

1. git clone 克隆远程仓库。
1. git config 配置。
1. git status 查看文件状态。
1. git diff 查看差异。
1. git add 暂存。
1. git reset 重置暂存区或工作区。
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

将文件放入暂存区。如匹配目录，则暂存目录下的所有文件。可用于跟踪新文件，暂存已修改文件，合并时将冲突标记为已解决：

```shell
git add PATTERN[ ...]
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

输入5或p，选择文件后，进入交互式部分暂存，可输入以下子命令：

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

进入交互式部分暂存：

```shell
git add -p|--patch
```

## git bisect

二分查找出有问题的提交。

## git blame

查看文件标注，可查看每一行分别来自那次提交：

```shell
git blame [-C] [-L STARTLINE,ENDLINE] NAME[ ...]
```

每行的结果包括以下内容：

1. 最后一次修改的提交哈希，以\^开头表示从未修改。
1. 原始文件名，当使用-C时出现，表示行内容从此文件复制过来。
1. 提交者。
1. 提交时间。
1. 文件行内容。

## git branch

创建分支。不会切换到新分支：

```shell
git branch BRANCH [COMMIT]
```

删除分支：

```shell
git branch -d BRANCH
```

强制删除分支。如果分支包含未合并的提交，则会将其丢弃：

```shell
git branch -D BRANCH
```

删除远程分支：

```shell
git branch -dr REMOTE/BRANCH
```

设置本地分支跟踪的远程分支。当设置好跟踪分支后，可以通过`@{upstream}`或`@{u}`快捷方式来引用它：

```shell
git branch -u|--set-upstream [BRANCH] REMOTE/BRANCH
```

查看所有本地分支。使用-v参数可查看每一个分支的最后一次提交。使用-vv参数可查看所有跟踪分支：

```shell
git branch
git branch -v
git branch -vv
```

查看所有远程分支：

```shell
git branch -r
```

查看所有本地分支和远程分支：

```shell
git branch -a
```

查看已合并到当前分支的分支：

```shell
git branch --merged
```

查看未合并到当前分支的分支：

```shell
git branch --no-merged
```

## git bundle

打包提交。

## git checkout

将HEAD、暂存区和工作区切换至指定的提交：

```shell
git checkout COMMIT
```

切换分支：

```shell
git checkout BRANCH
```

切换到上一个分支：

```shell
git checkout -
```

创建并切换分支：

```shell
git checkout -b BRANCH [COMMIT]
```

交互式检出文件：

```shell
git checkout --patch COMMIT
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

检出暂存区的文件至工作区：

```shell
git checkout [--] FILE[ ...]
```

检出某次提交的文件至工作区：

```shell
git checkout COMMIT FILE[ ...]
```

## git cherry-pick

将提交在当前分支重放，不影响原有提交：

```shell
git cherry-pick COMMIT
```

## git clean

清理工作区，移除未被追踪的文件：

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

编辑配置：

```shell
git config -e [--global]
```

设置配置：

```shell
git config [--global] alias.ALIAS SOURCE
git config [--global] core.autocrlf true|input|false
git config --bool core.bare true|false
git config [--global] core.editor EDITOR
git config [--global] core.quotepath true|false
git config [--global] credential.helper cache|store|osxkeychain
git config --system --unset credential.helper
git config [--global] gui.encoding ENCODING
git config [--global] http.sslVerify true|false
git config [--global] merge.conflictstyle merge|diff3
git config [--global] merge.tool MERGETOOL
git config [--global] mergetool.MERGETOOL.path PATH
git config [--global] pull.rebase true|false
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
* http.sslVerify：当使用HTTPS从远程仓库推送或拉取时是否使用SSL证书验证。当提示“OpenSSL SSL_read: Connection was reset, errno 10054”时可能需设置为false。
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
git commit [NAME[ ...]]
```

将已跟踪过的文件暂存并提交：

```shell
git commit -a
```

提交时注明备注信息：

```shell
git commit -m MSG
```

撤销上一次提交，重新提交：

```shell
git commit --amend
```

提交时跳过pre-commit和commit-msg的钩子：

```shell
git commit --no-verify
```

提交时显示差异信息：

```shell
git commit -v
```

## git credential

凭证辅助工具。

## git diff

比较工作区相对于暂存区的差异：

```shell
git diff
```

比较暂存区相对于已提交的差异：

```shell
git diff --staged|--cached
```

比较COMMIT2相对于COMMIT1的差异：

```shell
git diff COMMIT1 COMMIT2
```

比较时忽略空白符的差异：

```shell
git diff -b
```

比较时只列出有差异的文件名：

```shell
git diff --name-only
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
git fetch [REMOTE REMOTEBRANCH]
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

搜索文件内容，可搜索工作区和所有提交：

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
git VERB --help
man git-VERB
```

## git init

在当前目录初始化仓库。目录中可以存在文件。远程仓库最好使用--bare参数初始化，且之后仓库不能作为工作区：

```shell
git init [--bare]
```

在当前目录的子目录初始化仓库。

```shell
git init DIR
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
	* -p [FILENAME]：显示每次提交的内容差异，可只查看指定文件的差异。
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

将提交合并到当前分支：

```shell
git merge COMMIT
```

将提交合并到当前分支，总是创建一个提交而不使用快进（fast-forward）：

```shell
git merge --no-ff COMMIT
```

将没有共同祖先提交的不关联分支合并到当前分支：

```shell
git merge --allow-unrelated-histories COMMIT
```

合并时忽略空白：

```shell
git merge -Xignore-all-space|-Xignore-space-change COMMIT
```

* -Xignore-all-space： 完全忽略空白符的修改。
* -Xignore-space-change：将一个空白符与多个连续的空白字符视作等价的。

合并时冲突使用当前分支的内容：

```shell
git merge -Xours COMMIT
```

合并时冲突使用待合入分支的内容：

```shell
git merge -Xtheirs COMMIT
```

中断合并：

```shell
git merge --abort
```

如有合并冲突，需使用`git mergetool`解决，然后执行`git add`和`git commit`。

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
git push [REMOTE] [[COMMIT:]REMOTEBRANCH] [--force|-f]
```

如出现如下错误，则在远程仓库执行`git config --bool core.bare true`。

```
remote: error: refusing to update checked out branch: refs/heads/master
remote: error: By default, updating the current branch in a non-bare repository
```

推送所有分支到远程仓库：

```shell
git push --all [REMOTE]
```

推送的时候同时在远程仓库创建分支：

```shell
git push -u|--set-upstream REMOTE REMOTEBRANCH
```

在远程仓库删除分支：

```shell
git push REMOTE --delete REMOTEBRANCH
```

推送标签到远程仓库：

```shell
git push REMOTE TAG
```

推送所有标签到远程仓库：

```shell
git push REMOTE --tags
```

删除远程仓库的标签：

```shell
git push REMOTE :refs/tags/TAG
```

## git rebase

**不要对在你的仓库外有副本的分支执行变基。**变基操作的实质是丢弃一些现有的提交，然后相应地新建一些内容一样但实际上不同的提交。

变基。将当前分支变基到目标基底分支BRANCH，并应用当前分支相较于最近共同祖先的修改。一般这样做的目的是为了向远程分支推送时保持提交历史的整洁：

```shell
git rebase BRANCH
```

将特性分支BRANCHFROM变基到目标基底分支BRANCHTO：

```shell
git rebase BRANCHTO BRANCHFROM
```

取出BRANCHFROM分支，找出BRANCHFROM和BRANCH共同祖先之后的修改，然后在BRANCHTO分支上重放：

```shell
git rebase --onto BRANCHTO BRANCH BRANCHFROM
```

交互式变基，将指定提交及之后的提交重写：

```shell
git rebase -i COMMIT
```

其列出的提交历史是从旧至新的，修改提交历史前的命令，退出后`git commit --amend`修改`edit`命令指定的提交，`git rebase --continue`自动应用剩余的提交。

中断变基：

```shell
git rebase --abort
```

如有变基冲突，需使用`git mergetool`解决，然后执行`git add`和`git commit`。

处理完当前提交的变基冲突后，继续执行后续提交的变基：

```shell
git rebase --continue
```

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

恢复暂存区，但不改变工作区，相当于回滚`git add`：

```shell
git reset [NAME]
```

将HEAD指针、当前分支恢复至指定的提交，但不改变暂存区和工作区，相当于回滚`git commit`：

```shell
git reset --soft COMMIT [NAME]
```

将HEAD指针、当前分支、暂存区恢复至指定的提交，但不改变工作区，相当于回滚`git add`和`git commit`：

```shell
git reset [--mixed] COMMIT [NAME]
```

将HEAD指针、当前分支、暂存区、工作区恢复到指定的提交，如工作区有未提交的内容则会丢失：

```shell
git reset --hard COMMIT [NAME]
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

撤销指定提交的所有改动，会创建一个新提交：

```shell
git revert COMMIT
```

还原至合并前：

```shell
git revert [-m N] HEAD
```

N的值：1为还原至本地分支，2为还原至待合入分支。

## git rm

从暂存区移除跟踪文件，并从工作区删除文件。若文件已修改且已放入暂存区，则需要使用-f：

```shell
git rm [-f] NAME[ ...]
```

只从暂存区移除跟踪文件，不从工作区删除文件：

```shell
git rm --cached NAME[ ...]
```

## git show

查看提交的变更内容：

```shell
git show [COMMIT] [FILENAME]
```

查看提交中指定文件的完整内容：

```shell
git show [COMMIT]:FILENAME
```

查看提交的简略统计信息：

```shell
git show --stat [COMMIT]
```

查看提交中发生变化的文件：

```shell
git show --name-only [COMMIT]
```

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

* -a或--all：同时储藏未跟踪的文件和忽略跟踪的文件。
* --include-untracked或-u：同时贮藏未跟踪的文件，但不包括忽略跟踪的文件。
* --keep-index：不仅贮藏已暂存的文件，还将它们保留在索引中。

交互式部分储藏改动，可输入以下子命令：

```shell
git stash push -p|--patch
```

* y：暂存此块文件内容。
* n：不暂存此块文件内容。
* q：退出且不暂存此块及所有文件余下的内容。
* a：暂存此块及本文件中所有后续块的文件内容。
* d：不暂存此块及本文件中所有后续块的文件内容。
* s：将当前块分割成更小的块。
* e：手工编辑当前块。
* ?：查看帮助。

查看储藏栈，每条以stash@{N}标记，stash@{0}为栈顶，即最近的储藏：

```shell
git stash list
```

应用栈顶的储藏后移除该储藏。可以在一个不干净的工作区，或其它分支上应用储藏，但可能产生合并冲突：

```shell
git stash pop [--index]
```

应用储藏，如不指定储藏标记，则默认应用最近的储藏。可以在一个不干净的工作区，或其它分支上应用储藏，但可能产生合并冲突：

```shell
git stash apply [--index] [stash@{N}]
```

使用--index可同时应用暂存，否则只应用文件的改动，不会重新暂存。

移除储藏：

```shell
git stash drop stash@{N}
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

创建轻量标签（lightweight）：

```shell
git tag TAG [COMMIT]
```

创建附注标签（annotated）：

```shell
git tag -a TAG -m MSG [COMMIT]
```

删除标签：

```shell
git tag -d TAG
```
