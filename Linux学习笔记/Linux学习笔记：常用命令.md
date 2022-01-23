本文更新于2022-01-22

[TOC]

**说明：下文中，大写为自定义变量，根据实际情况填写（个别大写的参数除外）。使用`[]`引起表示内容可选，使用`{}`引起表示内容为一个整体，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

# 关机

## shutdown

关机或重启。

```shell
shutdown [-hkr] [TIME [MESSAGE]]
shutdown -c
```

TIME可为hh:mm的格式指定时间，或为+m的格式指定m分钟后，或为now表示现在。缺省为+1。

* -c：取消关机。
* -h：将系统服务停止后就关机。
* -k：不真正执行，只是发送警告消息。
* -r：将系统服务停止后就重启。

# 磁盘与分区

## blkid

查看块设备属性。

```shell
blkid
```

## blockdev

查看或设置块设备设置。

```
blockdev --report
blockdev --setra N DEVICE
```

* --report：查看块设备设置。显示结果各列的含义如下：
	* RO
	* RA：预读的扇区数量。
	* SSZ
	* BSZ
	* StartSec
	* Size：字节大小。
	* Device：设备名。
* --setra N：设置预读的扇区数量。

## df

列出文件系统的磁盘挂载点和空间使用情况。

```shell
df [-ahHikmT] [NAME]
```

* -a：列出所有的文件系统，包括系统特有的/proc等。
* -h：以人类易读的方式列出，如K。
* -H：类似-h，但以1000而不是1024为进制。
* -i：列出i节点数量，而不是磁盘容量。
* -k：以K为单位。
* -m：以M为单位。
* -T：同时列出文件系统类型，如ext3。

如指定文件或目录名，则列出其所在的文件系统；否则，列出所有文件系统。

显示结果各列的含义如下：

* Filesystem：使用的文件系统设备。
* 1K-blocks：大小为1K的块的数量。
* Size：总大小。
* Used：已用大小。
* Available：可用大小。
* Use%：使用率。
* Mounted on：挂载点目录名。

## du

列出文件或目录的磁盘使用情况。

```shell
du [-ahkmsS] NAME
```

当不使用-a、-s、-S时，列出所有目录，目录的计算方式为汇总其子目录和文件。

* -a：列出所有目录和文件的磁盘使用量，目录的计算方式为汇总其子目录和文件。
* -h：以人类易读的方式列出，如K。
* -k：以K为单位。
* -m：以M为单位。
* -s：只列出总量，不列出子目录，目录的计算方式为汇总其子目录和文件。
* -S：列出所有目录，目录的计算方式为只汇总文件，不汇总子目录。

## fdisk

MBR磁盘分区。最大支持2T的磁盘。

```shell
fdisk DEV
fdisk -l [DEV]
```

* -l：列出指定设备的分区信息。如未指定设备则列出所有设备的分区信息。

不带参数时为进行磁盘分区。

## lsblk

以树形结构查看块设备。

```shell
lsblk
```

## mkfs

格式化分区。

```shell
mkfs [-t FSTYPE] PARTITIONDEV
```

* -t FSTYPE：指定文件系统类型，如ext2、ext3、ext4。

## mount

挂载分区。

```shell
mount -a
mount [-l]
mount [-L LABEL] [-n] [-o OPTION] [-t FSTYPE] PARTITIONDEV DIR
```

不使用参数则列出当前的挂载信息。

* -a：依照/etc/fstab将所有未挂载的分区挂载。
* -l：同时列出卷标名（Label）。
* -L LABEL：指定卷标名，使用LABEL而不使用PARTITIONDEV进行挂载。
* -n：不将信息更新至/etc/mtab。
* -o OPTION：指定挂载的额外选项。可为如下值，以逗号分隔：

	* async：异步写入。
	* auto：允许被`mount -a`自动挂载。
	* defaults：默认值，为`auto,async,dev,exec,nouser,rw,suid`。
	* dev：允许在此分区上创建设备文件。
	* exec：允许在此分区上执行二进制文件。
	* noauto：不允许被`mount -a`自动挂载。
	* nodev：不允许在此分区上创建设备文件。
	* noexec：不允许在此分区上执行二进制文件。
	* nosuid：不允许在此分区上有SUID/SGID的文件。
	* nouser：不允许普通用户对此分区执行`mount`。
	* remount：重新挂载，在系统出错或重新更新选项时有用。
	* ro：只读。
	* rw：可读写。
	* suid：允许在此分区上有SUID/SGID的文件。
	* sync：同步写入。
	* user：允许普通用户对此分区执行`mount`。
* -t FSTYPE：指定文件系统类型。

## parted

磁盘分区。支持大于2T的磁盘。

```shell
parted [DEV]
```

使用如下子命令：

* mklabel LABELTYPE：转换分区格式。LABELTYPE为分区格式，可为gpt等。
* mkpart PARTTYPE [FSTYPE] START END：创建分区。PARTTYPE为分区类型，可为primary（主分区）等；FSTYPE为文件系统类型；START为开始位置，END为结束位置，默认以M为单位，可使用百分比N%。
* align-check TYPE PARTITIONDEV：检查分区是否对齐，显示“PARTITIONDEV aligned”表示已对齐。TYPE必须为minimal（最小）或optimal（最优）；PARTITIONDEV为分区的编号，从1开始。
* print：打印分区表。
* quit：退出`parted`。

## partprobe

令系统重新读取分区表。

```shell
partprobe
```

## umount

卸载分区。

```shell
umount [-fn] PARTITION
```

PARTITION可为设备名或挂载目录名。

* -f：强制卸载。
* -n：不将信息更新至/etc/mtab。

# 文件与目录

## basename

显示路径的文件名/目录名。

```shell
basename PATH
```

## cd

切换目录。cd实际上是shell内置的命令。

```shell
cd [DIR]
```

dir可取：

* 缺省：当前用户主目录。
* .：当前目录。
* ..：当前目录的上级目录。
* -：前一个工作目录。
* ~：当前用户主目录。
* ~USER：USER用户的主目录。
* PATH：绝对路径或相对路径。

## cp

复制文件/目录。

```shell
cp [-adfprR] SRC[ ...] DST
```

* -a：相当于-dpr。
* -d：若源文件为连接文件，复制的目标文件同样为连接文件。
* -f：强制复制。
* -p：连同文件的属性一起复制。
* -r,-R：递归复制子目录和文件。

若DST为目录，则将SRC复制进目录中。若有多个SRC，则DST必须为目录。

## chgrp

修改文件/目录的用户组。

```shell
chgrp [-R] GROUP NAMEGLOB[ ...]
```

* -R：递归更改子目录和文件。

## chmod

修改文件/目录的权限。

```shell
chmod [-R] MODE NAMEGLOB[ ...]
```

* -R：递归更改子目录和文件。

MODE可取如下形式：

* 数字类型: 如0770。
* 符号类型：{{ugoa}{=+-}{rwxst}}[,...]，a表示所有，s只能用于u（SUID）g（SGID），t只能用于o（SBIT）。如u=rw,go=r、a+w,o-x。

## chown

修改文件/目录的所有者。

```shell
chown [-R] [GROUP:]OWNER NAMEGLOB[ ...]
```

* -R：递归更改子目录和文件。

## dd

复制文件/设备。

```shell
dd [if=INPUTFILE] [of=OUTPUTFILE] [bs=BLOCKSIZE] [count=COUNT]
```

* bs：块大小，默认为512字节（一个扇区的大小）。可使用K/M/G格式。
* count：块数量。
* if：输入文件，可以为设备。
* of：输出文件，可以为设备。

## file

查看文件类型。

```shell
file FILENAME
```

## ln

创建连接文件。

```shell
ln [-fs] SRC DST
```

* -f：强制创建。
* -s：创建软连接，缺省则为创建硬连接。

DST为新建的文件。

## ls

查看文件/目录。

```shell
ls [-aAdFhlrRSt --full-time --time={atime|ctime}] [NAMEGLOB]
```

* -a：列出全部文件，包括隐藏文件。
* -A：列出全部文件，包括隐藏文件，但不包括.和..。
* -d：列出目录本身，而不是列出目录内的文件。
* -F：文件名附加文件类型信息，*为可执行文件，/为目录，=为套接字文件，|为管道文件。
* -h：将文件大小以人类易读的方式列出，如K。
* -l：列出长串格式。
* -r：将结果反向排序。
* -R：递归列出子目录和文件。
* -S：以文件大小排序。
* -t：以时间排序。
* --full-time：列出完整的时间。
* --time={atime|ctime}：指定时间类型，不使用默认的mtime。

## mkdir

新建目录。

```shell
mkdir [-mp] DIR[ ...]
```

* -m：指定目录的权限，会忽略umask的值。
* -p：递归创建父目录。

## mv

移动或重命名文件/目录。

```shell
mv [-f] SRC[ ...] DST
```

* -f：强制移动或重命名。

若DST为目录，则将SRC移动进目录中。若有多个SRC，则DST必须为目录。

## pwd

显示当前目录。

```shell
pwd
```

## rm

删除文件/目录。

```shell
rm [-frR] NAMEGLOB
```

* -f：强制删除。
* -r,-R：递归删除子目录和文件。

## rmdir

删除空目录。

```shell
rmdir [-p] DIR
```

* -p：递归删除空的父目录，如`rm a/b/c`可递归删除a。

## test

检测文件和比较值。

```shell
test OPTEST
[ OPTEST ]
```

若为真，则命令执行返回码为0；否则为1。注意，上述两种表示是等价的，第二种`[`和`]`间必须有空格。OPTEST中的变量和常量建议使用`""`引起，使用方法如下：

检测文件或目录，如`test OP NAME`：

* -b：检测是否存在且为块设备。
* -c：检测是否存在且为字符设备。
* -d：检测是否存在且为目录。
* -e：检测是否存在。
* -f：检测是否存在且为文件。
* -L：检测是否存在且为连接文件。
* -s：检测是否存在且大小不为0。
* -S：检测是否存在且为套接字（Socket）文件。
* -p：检测是否存在且为管道（FIFO）文件。

检测文件或目录权限（但root用户常有例外），如`test OP NAME`：

* -g：检测是否存在且有SGID属性。
* -k：检测是否存在且有SBIT属性。
* -r：检测是否存在且有读权限。
* -u：检测是否存在且有SUID属性。
* -w：检测是否存在且有写权限。
* -x：检测是否存在且有执行权限。

文件或目录比较，如`test NAME1 OP NAME2`：

* -ef：比较是否同一个文件。可用于检测硬连接，如指向同一个inode则为同一个文件。
* -nt：比较NAME1是否比NAME2新。
* -ot：比较NAME1是否比NAME2老。

整数比较，如`test N1 OP N2`：

* -eq：比较是否相等。
* -ge：比较N1是否大于等于N2。
* -gt：比较N1是否大于N2。
* -le：比较N1是否小于等于N2。
* -lt：比较N1是否小于N2。
* -ne：比较是否不等。

字符串比较，如`test OP STR`：

* -n：比较字符串是否不为空串。`-n`也可省略。
* -z：比较字符串是否为空串。

或：

* test STR1 = STR2：比较字符串是否相等。`=`也可为`==`。
* test STR1 != STR2：比较字符串是否不等。

也可以对`test`进行多重判定：

* !：取反，如`test ! OPTEST`。
* -a：两个条件同时成立，如`test OPTEST1 -a OPTEST2`。
* -o：任意一个条件成立，如`test OPTEST1 -o OPTEST2`。

## touch

创建新文件或修改文件时间。

```shell
touch [-acmt] FILENAME
```

* -a：仅修改atime。
* -c：仅修改ctime，若该文件不存在会创建文件。
* -m：仅修改mtime。
* -t：指定时间，格式为[[CC]YY]MMDDhhmm[.ss]。

## umask

显示或设置权限掩码。umask实际上是shell内置的命令。

```shell
umask [MODE]
```

# 文件查找

## find

查找文件。

**根据时间查找：**

```shell
find dir[ ...] -atime T
find dir[ ...] -ctime T
find dir[ ...] -mtime T
find dir[ ...] -newer NAME
```

* -atime T：列出atime在指定时间被更新过的文件。
* -ctime T：列出ctime在指定时间被更新过的文件。
* -mtime T：列出mtime在指定时间被更新过的文件。
* -newer NAME：使用文件或目录比较，列出比NAME的mtime更新的文件。

T可取如下形式：

* N：距离当前时刻N天之前的24小时内。
* +N：距离当前时刻N天（不含）之前。
* -N：距离当前时刻N天（含）之内。

**根据用户或用户组查找：**

```shell
find dir[ ...] -uid UID
find dir[ ...] -gid GID
find dir[ ...] -user USERNAME
find dir[ ...] -group GROUPNAME
find dir[ ...] -nouser
find dir[ ...] -nogroup
```

* -uid UID：列出文件的所有者ID为UID的文件。
* -gid GID：列出文件的用户组ID为GID的文件。
* -user USERNAME：列出文件的所有者为USERNAME的文件。
* -group GROUPNAME：列出文件的用户组为GROUPNAME的文件。
* -nouser：列出文件的所有者不在/etc/passwd中的文件。
* -nogroup：列出文件的用户组不在/etc/group中的文件。

**根据文件权限查找：**

```shell
find dir[ ...] -perm MODE
find dir[ ...] -perm +MODE
find dir[ ...] -perm -MODE
```

* -perm MODE：列出文件权限等于MODE的文件。
* -perm +MODE：列出文件权限包含MODE全部权限位的文件。
* -perm -MODE：列出文件权限包含MODE任一权限位的文件。

**其他条件查找：**

```shell
find dir[ ...] -name NAME
find dir[ ...] -size +SIZE
find dir[ ...] -size -SIZE
find dir[ ...] -type TYPE
```

* -name NAME：列出文件名为NAME的文件或目录，可使用通配符匹配。
* -size +SIZE：列出大于SIZE（不含）的文件，使用c表示1字节，k表示1024字节。
* -size -SIZE：列出小于SIZE（不含）的文件，使用c表示1字节，k表示1024字节。
* -type TYPE：列出指定文件类型的文件，f为一般文件，b/c为设备文件，d为目录，l为连接文件，s为SOCKET文件，p为FIFO文件。

## type

查看命令的实际名称（别名或绝对路径等）。type实际上是shell内置的命令。

```shell
type [-a] COMMAND
```

* -a：列出所有COMMAND命令。

## whereis

查看包含指定文件名（不含扩展名）的二进制文件、源代码文件和man手册文件的绝对路径。

```shell
whereis FILENAME
```

## which

查看命令的绝对路径。

```shell
which [-a] COMMAND
```

* -a：列出所有COMMAND命令。

# 文件内容查看

## cat

正向显示全部内容。

```shell
cat [-AbEnTv] FILENAME[ ...]|STDIN
```

* -A：相当于-vET。
* -b：显示行号，仅针对非空白行。
* -E：将换行符显示为$。
* -n：显示行号，包括空白行。
* -T：将Tab显示为^I。
* -v：显示不可见的特殊字符。

## head

显示前几行。

```shell
head [-n COUNT] FILENAME[ ...]|STDIN
```

* -n：显示前COUNT行。若COUNT以-开头，则显示至倒数第COUNT（不含）行。

## less

可翻页显示。

```shell
less FILENAME[ ...]|STDIN
```

只可使用管道输入时使用STDIN。

在显示界面，可输入如下命令：

* Enter键：下滚一行。
* PageUp键：上翻一页。
* PageDown键：夏凡一页。
* Space键：下翻一页。
* /str：向下查询str。
* ?str：向上查询str。
* n：重复前一个查询。
* N：反向重复前一个查询。
* q：离开。

## more

向后翻页显示。

类似less，但不可向前翻页。推荐使用less。

```shell
more FILENAME[ ...]|STDIN
```

## nl

添加行号显示。

```shell
nl [-b a|t -n ln|rn|rz -w N] FILENAME[ ...]
```

* -b a|t：指定显示的行。a为所有行包括空行，t为忽略空行。
* -n ln|rn|rz：指定行号的显示方式。ln为左对齐，rn为右对齐不补前导0，rz为右对齐补前导0.
* -w N：指定行号占用的字符宽度。

## od

显示二进制。

```shell
od [-t TYPE] FILENAME[ ...]|STDIN
```

* -t：指定显示的类型。
	* a：默认。
	* c：ASCII。
	* d[SIZE]：十进制，每个数占用SIZE字节。
	* f[SIZE]：浮点数，每个数占用SIZE字节。
	* o[SIZE]：八进制，每个数占用SIZE字节。
	* x[SIZE]：十六进制，每个数占用SIZE字节。
	
输出的第一列表示该行的第一个字节是该文件的第几个字节（八进制，以0开始）。

## strings

显示文件中的可打印字符。可用于二进制文件。

```shell
strings FILENAME
```

## tac

反向显示全部内容。

```shell
tac FILENAME[ ...]|STDIN
```

## tail

显示后几行。

```shell
tail [-f -n COUNT] FILENAME[ ...]|STDIN
```

* -f：持续检测文件。
* -n：显示后COUNT行。若COUNT以+开头，则从第COUNT（含）行开始。

# 文本处理

## awk

以行为单位操作文本列。

```shell
awk [-F 'REGEXP'] '[CONDITION] {ACTION} [[CONDITION] {ACTION} ...]' FILENAME[ ...]|STDIN
```

* -F 'REGEXP'：指定分隔符，以正则表达式匹配，支持扩展正则表达式。默认为任意个（大于等于1个）Space或Tab。

CONDITION和ACTION中的字符串都需要用""括起。可使用逻辑运算符（<、>、<=、>=、==、!=），赋值运算符（=）。

CONDITION可使用的特殊值：BEGIN、END。

ACTION有多个命令，可使用;或Enter键隔开。ACTION可使用的函数：

* print：`print ARG[ ...]`，没有空格分隔，自动换行。
* printf：`printf "FORMAT"[, ARG, ARG ...]`，不自动换行。FORMAT的变量格式如下：
	* %[N]s：占位为N的字符串。
	* %[N]d：占位为N的十进制整数。
	* %[N][.M]f：占位为N小数位为M的浮点数。
	* %[N]x：占位为N的十六进制整数。

自定义变量不需要加$。内置变量如下：

* $N：第N列的内容，从1开始。$0表示整行。
* NF：列数量。
* NR：目前的行号，从1开始。
* FS：目前的分隔符。

示例：

```shell
awk -F ':' 'BEGIN {total = 0} NR <= 5 {total = total + NF; printf "%d %d %10s %s\n", total, NF, $1, $0}' /etc/passwd
```

## cmp

以字节为单位比较二进制内容。

```shell
cmp [-bl] FILENAME1 FILENAME2
```

* -b：显示字节对应的字符。
* -l：列出所有不同的字节。

## cut

以行为单位切割文本列。

```shell
cut -d DELIMITER -f RANGES FILENAME[ ...]|STDIN
cut -c RANGES FILENAME[ ...]|STDIN
```

* -c：以字符为单位。
* -d DELIMITER：以DELIMITER分隔进行切割，输出如有多列也以DELIMITER分隔。DELIMITER只能是一个字符，默认为Tab。
* -f RANGES：取出列的范围。RANGES的格式为{N|N-M|N-|-M}[,...]，第一列为1。如：2、-2,5,7,9-10,12-。

## diff

以行为单位比较文本。

```shell
diff [-bBi] FROMNAME TONAME
```

* -b：忽略一行中多个空白符的区别。
* -B：忽略空白行的区别。
* -i：忽略大小写。

结果的含义：

* {N1}a{N3[,N4]}：FROMNAME的N1行后添加TONAME的N3（含）至N4（含）行。
* {N1[,N2]}d{N3}：TONAME的N3行后删除FROMNAME的N1（含）至N2（含）行。
* {N1[,N2]}c{N3[,N4]}：FROMNAME的N1（含）至N2（含）行修改为TONAME的N3（含）至N4（含）行。
* <：从FROMNAME删除的行。
* >：添加到TONAME的行。

## egrep

类似`grep -E`，参看grep。

## grep

查找匹配字符串的行。

```shell
grep [-acEinrsv -A N -B N -C N --color] 'REGEXP' {FILENAME|DIR}[ ...]|STDIN
```

* -N：同时显示匹配行的前后N行。N为数字。
* -a：将二进制文件以文本文件方式查找。
* -A N：同时显示匹配行的后N行。
* -B N：同时显示匹配行的前N行。
* -C N：同时显示匹配行的前后N行。
* -c：显示匹配的行数。
* -E：使用扩展正则表达式。
* -i：忽略大小写。
* -n：同时显示行号。
* -r：递归匹配目录中的文件。等同于使用：-d recurse。
* -s：不显示错误消息。
* -v：反向显示不匹配的行。
* --color：将匹配内容着色显示。

REGEXP最好用''或""括起来，虽然一些简单正则表达式缺省也无问题。

正则表达式：

* \^：行首。
* $：行尾。
* .：任意一个字符。
* \\：转义字符。
* *：重复前一个0次或多次。
* [AB]：枚举中任意一个。
* [A-B]：范围中任意一个。
* [\^AB]：不在枚举中的任意一个。
* [\^A-B]：不在范围中的任意一个。
* {N}：重复前一个N次。
* {N,}：重复前一个N次或更多。
* {N,M}：重复前一个最少N次（含），最多M次（含）。

以下特殊字符需配合[]一起使用，如：[\^[:lower:]]：

* [:alnum:]：字母和数字。
* [:alpha:]：字母。
* [:blank:]：Space键和Tab键。
* [:cntrl:]：控制键。
* [:digit:]：数字。
* [:graph:]：除Space键和Tab键外的所有键。
* [:lower:]：小写字母。
* [:print:]：可打印字符。
* [:punct:]：标点符号。
* [:upper:]：大写字母。
* [:space:]：空白符。
* [:xdigit:]：十六进制数字。

扩展正则表达式：

* +：重复前一个1次或多次。
* ?：重复前一个0次或1次。
* A|B：A或B任意一个。注意，若abc和xyz都是字符串，则abc|xyz匹配abc或xyz。
* ()：分组。

## iconv

字符编码转换。

```shell
iconv --list
iconv -f FROM -t TO FILENAME [-o NEWFILENAME]
```

* -f：原来的字符编码。
* -o：如需保留原来的文件，则指定新文件。
* -t：后来的字符编码。
* --list：列出支持的所有字符编码。

## join

将文本以关联列拼贴在一起。

```shell
join [-i -t DELIMITER -1 N -2 M] FILENAME1 FILENAME2
```

* -1 N：指定FILENAME1的关联列。
* -2 M：指定FILENAME2的关联列。
* -i：忽略大小写。
* -t DELIMITER：指定文本列的分隔符，默认为Tab。拼贴后两文本的内容也以DELIMITER分隔。

## paste

直接按顺序将文本拼贴在一起，中间以Tab分隔。

```shell
paste [-d] FILENAME1 FILENAME2
```

* -d：指定文本列的分隔符，默认为Tab。

## printf

格式化打印。

```shell
printf 'FORMAT' ARG[ ...]
```

会循环使用FORMAT格式化参数。

FORMAT的变量格式如下：

* %[N]s：占位为N的字符串。
* %[N]i：占位为N的整数。
* %[N.M]f：占位为N小数位为M的浮点数。

## sed

以行为单位编辑内容。

```shell
sed [-nri] '[N1[,N2]] a CONTENT' FILENAME[ ...]|STDIN
sed [-nri] '[N1[,N2]] c CONTENT' FILENAME[ ...]|STDIN
sed [-nri] '[N1[,N2]] d' FILENAME[ ...]|STDIN
sed [-nri] '[N1[,N2]] i CONTENT' FILENAME[ ...]|STDIN
sed [-nri] '[N1[,N2]] p' FILENAME[ ...]|STDIN
sed [-nri] 's/OLDREGEXP/NEWREGEXP/g' FILENAME[ ...]|STDIN
```

* -n：安静模式，只显示经过sed处理的行。
* -r：使用扩展正则表达式。
* -i：直接修改文件的内容。

操作的行包括N1和N2。N1和N2都缺省表示所有行。如有多个文件，则将所有文件组合在一起再计算行号。

操作的含义：

* a：每行后新增。
* c：整体替换行（注意，不是每行替换一次）。
* d：删除行。
* i：每行前插入。
* p：显示行，通常和-n一起使用。
* s：替换内容。

CONTENT可使用\来输入多行。

## split

切割文件。

```shell
split [-b N] [-C N] [-l N] FILENAME|- [FILEPREFIX]
```

* -b N：每个文件N字节。
* -C N：每个文件N字节，尽量保证行的完整性。
* -l N：每个文件N行。

如不使用FILENAME，则使用-表示标准输入。

FILEPREFIX缺省为x。切割出来的文件名为FILEPREFIX拼接上小写英文字母生成的有序字符串。

## sort

排序文本行。

```shell
sort [-bfnru -k n -t DELIMITER] FILENAME[ ...]|STDIN
```

* -b：忽略最前面的空格。
* -f：忽略大小写。
* -n：以数值进行排序。
* -k n：指定排序的列，从1开始，列以-t指定的分隔符分隔。可使用多个选项指定多列排序，但需与其他指定排序列的选项连写，如：-k1nr -k2n。
* -r：反向排序。
* -t DELIMITER：分隔符，默认为Tab。
* -u：相同的文本只出现唯一一行。

## tr

字符操作。

```shell
tr [-s] OLDSET NEWSET STDIN
tr -s SET STDIN
tr -d SET STDIN
```

* -d：删除集合中的字符。
* -s：将连续出现的集合中的字符替换成一个字符。

OLDSET使用abc形式时，NEWSET也可使用abc形式，且会一一对应替换。如NEWSET不足，则余下的使用NEWSET最后的字符替换。
OLDSET使用a-z或[a-z]形式时，NEWSET最好使用单个字符。

## uniq

行内容排重。

```shell
uniq [-ci] FILENAME|STDIN
```

* -c：显示重复的次数。
* -i：忽略大小写。

## vi

编辑文本。

```shell
vi FILENAME[ ...]
```

## vim

编辑文本。`vi`的升级版本。

```shell
vim FILENAME[ ...]
```

## wc

文本内容统计。

```shell
wc [-lmw] FILENAME[ ...]|STDIN
```

* -l：显示行数。
* -m：显示字节数。
* -w：显示单词数。

不带参数时依次显示：行数、单词数、字节数、文件名。

# 压缩与打包

## tar

打包或解包tar文件。

```shell
tar -c [-j|-z -v] -f FILENAME [-C DIR] NAME[ ...]
tar -t [-j|-z -v] -f FILENAME
tar -x [-j|-z -v] -f FILENAME [-C DIR]
```

* -c：打包文件，指定的NAME即为包中文件名。
* -C DIR：指定打包文件的源目录或解包文件的目标目录，目录必须存在。默认为当前目录。
* -f FILENAME：指定打包文件的目标文件名或解包文件的源文件名。
* -j：通过bzip2压缩/解压缩，文件名最好为*.tar.bz2。
* -t：查看包中文件。
* -v：显示正在打包/解包的文件名。
* -x：解包文件。
* -z：通过gzip压缩/解压缩，文件名最好为*.tar.gz。

## unzip

解压缩zip文件。

```shell
unzip -l FILENAME
unzip FILENAME
```

* -l：查看压缩包中文件。

# 用户与用户组

## adduser

增加用户。

RHEL系列为`useradd`的软连接，参看其使用。Debian系列此命令较`useradd`更易用。

```shell
adduser USERNAME
```

## groupadd

增加用户组。

```shell
groupadd [-r] GROUPNAME
```

* -r：增加系统用户组。

## groupmod

修改用户组。

```shell
groupmod [-n NEWGROUPNAME] GROUPNAME
```

* -n NEWGROUPNAME：修改用户组名。

## groupdel

删除用户组。

```shell
groupdel GROUPNAME
```

## groups

查看所属用户组。

```shell
groups [USERNAME[ ...]]
```

第一个为有效用户组。

## gpasswd

用户组管理员功能。

```shell
gpasswd GROUPNAME
gpasswd [-r -A USERNAME[,...]] [-M USERNAME[,...]] GROUPNAME
gpasswd [-a USERNAME -d USERNAME] GROUPNAME
```

不使用选项时为给用户组设置密码。

* -a USERNAME：添加用户至用户组，用户组管理员可使用此选项。
* -A USERNAME[,...]：添加用户作为用户组管理员。
* -d USERNAME：从用户组删除用户，用户组管理员可使用此选项。
* -M USERNAME[,...]：添加用户至用户组。
* -r：删除用户组密码。

## id

查看UID/GID等信息。

```shell
id [USERNAME]
```

## last

以时间排序显示各次登录信息。

```shell
last
```

## lastlog

显示各用户的最后登录时间。

```shell
lastlog [-u USERNAME]
```

* -u USERNAME：指定用户名。

## newgrp

切换有效用户组。

```shell
newgrp GROUPNAME
```

## passwd

修改密码。

```shell
passwd [--stdin] [USERNAME]
passwd [-lu] USERNAME
```

* -l：锁定密码，令其无法登录。
* -u：解锁密码。
* --stdin：从stdin读取密码，缺省是使用交互命令输入密码。

## su

切换用户。

```shell
su [- -lmp -c COMMAND] USERNAME
```

* -,-l：使用login shell方式登录，会读取新用户的环境变量。
* -c COMMAND：以新用户身份执行命令。
* -m,-p：保留原用户的环境变量。

## sudo

以其他用户身份执行命令。

```shell
sudo [-b -u USERNAME] COMMAND
sudo -s
```

* -b：将命令在后台执行。
* -u USERNAME：指定欲切换的用户，缺省为root。
* -s：切换至root，并执行环境变量SHELL或/etc/passwd所指定的shell。

## useradd

增加用户。

Debian系列建议使用`adduser`。

```shell
useradd [-DmMr -g GROUPNAME -G GROUPNAME[,...]] USERNAME
```

* -D：显示增加用户的默认值。
* -g GROUPNAME：指定初始用户组名。
* -G GROUPNAME[,...]：指定次要用户组名。
* -m：创建主目录（普通用户默认值）。
* -M：不创建主目录（系统用户默认值）。
* -r：增加为系统用户。

## userdel

删除用户。

```shell
userdel [-r] USERNAME
```

* -r：连同主目录一起删除。

## usermod

修改用户。

```shell
usermod [-aLU -g GROUPNAME -G GROUPNAME[,...] -l NEWUSERNAME] USERNAME
```

* -a：与-G配合，可追加次要用户组。
* -g GROUPNAME：修改初始用户组。
* -G GROUPNAME[,...]：修改次要用户组。
* -l NEWUSERNAME：修改用户名，并不会同时修改主目录名。
* -L：锁定用户。
* -U：解锁用户。

## visudo

修改/etc/sudoers。

```shell
visudo
```

/etc/sudoers使用制表符分隔各字段：

> 用户名或组名	主机名=(可切换的用户名)	命令的绝对路径及其参数正则表达式

* 每个字段都可使用ALL表示所有。
* 组名需在前面加"%"。
* 命令前加"NOPASSWD: "可免去输入密码。
* 命令前加"!"表示不可执行。
* 多条命令使用", "分隔。
* 可使用"\\"来输入多行。

需按Ctrl+O然后按Enter保存，按Ctrl+X退出。

## w

显示当前登录的用户及其正在执行的命令。

```shell
w
```

## who

显示当前登录的用户。

```shell
who
```

# 工作

## bg

将后台工作在后台运行，bg实际上是shell内置的命令。

```shell
bg %JOBNUMBER
```

类似使用&，区别于使用ctrl+z（将前台工作放至后台暂停）。

## crontab

配置例行性工作调度。

```shell
crontab [-elr -u USERNAME]
```

* -e：编辑。
* -l：显示。
* -r：删除所有，如需删除一项则使用-e。
* -u USERNAME：指定用户的例行性工作调度，只有root能使用此选项。

每行包括的列：分、时、日、月、星期、命令。命令要使用绝对路径。时间相关的列含义如下：

* *：任何时刻都执行。
* N：指定时刻执行。
* N,M：指定多个时刻执行
* N-M：指定时间段执行。
* /X：每隔一定时间间隔执行，配合*/X，N-M/X等实现。

## fg

将后台工作取至前台处理，fg实际上是shell内置的命令。

```shell
fg [[%]JOBNUMBER]
fg -
```

缺省JOBNUMBER表示取出+代表的工作，使用-表示取出-表示的工作。

## jobs

查看后台工作，jobs实际上是shell内置的命令。

```shell
jobs [-lrs]
```

* -l：同时列出进程PID。
* -r：仅列出后台正在运行的工作。
* -s：仅列出后台暂停的工作。

带+代表最近被放至后台的工作，带-代表最后第二个被放至后台的工作。

## nohup

使工作与终端机无关，能脱机运行。

```shell
nohup COMMAND
```

nohup不支持shell内置的命令。通常与&配合使用。

# 进程

## kill

向进程发送信号，可杀死进程。

```shell
kill -l
kill [-SIGNAL] %JOBNUMBER[ ...]
kill [-SIGNAL] PID[ ...]
```

* -l：列出可以使用的信号。
* -SIGNAL：指定信号，SIGNAL为信号值（-l列出的数值）或信号名（-l列出的名称）。常用的信号如下：
	* 2(SIGINT)：类似键盘输入ctrl+c。
	* 9(SIGKILL)：强制杀死进程。
	* 15(SIGTERM)：以正常方式终止进程，为缺省值。
	* 17(SIGSTOP)：类似键盘输入ctrl+z，暂停进程。

## killall

使用运行进程的命令名向进程发送信号，可杀死进程。

```shell
kill [-iI -SIGNAL] COMMANDNAME[ ...]
```

* -i：使用交互命令确认。
* -I：忽略大小写。
* -SIGNAL：指定信号，SIGNAL为信号值或信号名。

## pidof

根据进程名查找进程ID。

```shell
pidof [-s] COMMAND
```

* -s：只列出一个进程ID而不是所有进程ID。

## pidstat

查看进程状态。

```shell
pidstat [-urd -p PID]
```

* -d：查看磁盘I/O状态。
* -p PID：指定进程ID。
* -r：查看内存使用状态。
* -u：查看CPU使用状态。

## pmap

查看进程中各个模块占用内存的情况。

```shell
pmap PID
```

## ps

查看进程状态信息。

```shell
ps [-q PID[,...]]
ps -l
ps -Al
ps -elf
ps aux
ps afjx
```

* a：列出所有与控制台有关的进程（所有用户，所有控制台）。
* f：显示进程树。
* j：格式化参数，以工作控制格式显示。
* u：格式化参数，以用户信息格式显示。
* x：列出当前用户的所有进程，如果和a一起使用则列出系统所有进程。
* -A：列出系统所有进程，同-e。
* -e：列出系统所有进程，同-A。
* -f：格式化参数，以全格式显示。如果和-l一起使用则显示命令的参数。
* -l：格式化参数，以较长的格式显示。
* -q PID[,...]：显示指定PID的进程。

格式化参数只能使用一个。

不加任何选项则只列出当前bash相关的进程。

显示结果各列的含义如下：

* %CPU：CPU使用率。
* %MEM：物理内存使用率，即RSS的比率。
* ADDR：进程在内存的那个部分，如果运行中则显示-。
* C：CPU使用率。
* CMD：运行进程的命令。
* F：进程标志，说明进程的权限。有：
	* 1：该进程只可执行fork，不可执行exec。
	* 4：root权限。
* NI：执行优先级的Nice值，越小优先级越高。
* PGID：进程组ID。
* PID：进程ID。
* PPID：父进程ID。
* PRI：执行优先级的Priority值，越小优先级越高。
* RSS：使用的常驻内存，单位为K。
* S：进程状态。有：
	* D：睡眠但不可被唤醒，如等待I/O。
	* R：运行中。
	* S：睡眠但可被唤醒。
	* T：停止。
	* Z：僵尸，进程已终止但无法被删除。
* SID：会话ID。
* START：进程启动的时间。
* STAT：进程状态，第一个字母与S相同。
* STIME：进程的启动时间。
* SZ：使用的内存页数量，SZ*内存页大小（以K为单位）=VSZ。
* TIME：使用的CPU累积时间。
* TPGID：进程关联的TTY的进程组ID，-1表示不关联TTY。
* TTY：终端机，如果有远程登录则为动态终端接口（pts/n）。
* UID：用户ID。
* USER：进程的用户，与UID对应。
* VSZ：使用的虚拟内存大小，单位为K。
* WCHAN：进程睡眠时执行至的函数名，如果运行中则显示-。

## pstree

查看进程树。

```shell
pstree [-ApuU]
```

* -A：进程树以ASCII字符连接。
* -p：同时列出进程ID。
* -u：同时列出进程用户名。
* -U：进程树以UTF-8字符连接。

## top

动态查看进程状态。

```shell
top [-bH -d SECONDS -n TIMES -p PID[,...]]
```

* -b：以批次方式执行，会将各次的结果顺序依次显示。
* -d SECONDS：刷新的时间间隔，单位为秒，缺省为5秒。
* -H：查看线程状态。
* -n TIMES：执行的次数。
* -p PID[,...]：只显示指定pid的进程。

界面上部为系统整体资源使用状态:

* 第一行：当前的时间，系统开机至今的时长，当前登录的用户数，最近1/5/15分钟的平均工作负载（即在特定的时间间隔内队列中运行的平均进程数）。
* 第二行：进程的总数，运行、睡眠、停止、僵尸进程的数量。
* 第三行：CPU负载。
	* us：不改变优先级的用户时间（time running un-niced user processes）。
	* sy：内核时间（time running kernel processes）。
	* ni：改变优先级的用户时间（time running niced user processes）。
	* id：空闲时间（time spent in the kernel idle handler）。
	* wa：I/O等待时间（time waiting for I/O completion）。
	* hi：硬中断时间（time spent servicing hardware interrupts）。
	* si：软中断时间（time spent servicing software interrupts）。
	* st：虚拟机偷取的时间（time stolen from this vm by the hypervisor）。
* 第四行：物理内存使用情况（单位为K），包括所有的、未分配的、已分配的、用作缓存的。
* 第五行：内存交换空间使用情况（单位为K），包括所有的、未分配的、已分配的。以及不使用内存交互空间可使用的物理内存数量（某些发行版）。
* 第六行：用于输入命令。

界面下部为进程的资源使用状态：

* PID：进程ID。
* USER：进程的用户。
* PR：执行优先级的Priority值，越小优先级越高。
* NI：执行优先级的Nice值，越小优先级越高。
* VIRT：使用的虚拟内存大小，单位为K，与ps的VSZ相同。
* RES：使用的常驻内存，单位为K，与ps的RSS相同。
* SHR：共享内存大小，单位为K。
* S：进程状态。
* %CPU：CPU使用率。
* %MEM：物理内存使用率，即RSS的比率。
* TIME+：使用的CPU累积时间。
* COMMAND：运行进程的命令。

top执行过程中可使用如下命令（以下所述排序均为降序），按ESC可退出交互的命令输入：

* ?：显示可输入的命令。
* 1：切换平均工作负载的显示，即第一行内容。
* k：向进程发送信号。会提示输入进程ID和信号。
* m：切换内存信息的显示，即第四行和第五行内容。
* M：以内存使用率（%MEM）排序。
* N：以PID（PID）排序。
* P：以CPU使用率（%CPU）排序，默认排序字段。
* r：设置进程的优先级。会提示输入进程ID和优先级。
* s：修改刷新的时间间隔。会提示输入秒数。
* t：切换工作和CPU的状态显示，即第二行和第三行内容。
* T：以使用的CPU累积时间（TIME+）排序。
* u：显示指定用户的进程。会提示输入用户名。
* q：离开top。

# 服务

## service

服务管理。新系统应使用`systemctl`。

```shell
service SERVICE restart
service SERVICE start
service SERVICE status
service SERVICE stop
```

子命令：

* restart：重启服务。
* start：启动服务。
* status：查看服务状态。
* stop：停止服务。

## systemctl

systemd系统服务管理。

单元文件存放目录按优先级从高至低为：`/etc/systemd/system/`（系统管理员安装的单元）、`/usr/lib/systemd/system/`或`/lib/systemd/system/`（软件包安装的单元）。

```shell
systemctl enable UNIT
systemctl daemon-reload
systemctl disable UNIT
systemctl is-enabled UNIT
systemctl list-unit-files
systemctl [list-units] [--all --failed --state STATE[,...] --type TYPE]
systemctl mask UNIT
systemctl reload UNIT
systemctl restart UNIT
systemctl start UNIT
systemctl status [UNIT]
systemctl stop UNIT
systemctl umask UNIT
```

子命令：

* enable：设置单元开机自动启动。
* daemon-reload：重新加载systemd。扫描新的或有变动的单元配置。
* disable：取消单元开机自动启动。
* is-enabled：查看单元是否未开机自动启动。
* list-unit-files：查看所有已安装单元。
* list-units：查看单元。默认子命令。
* mask：禁用单元。禁用后，也不能间接启动。
* reload：重新加载单元配置。
* restart：重启单元。
* start：启动单元。
* status：查看单元状态。如不指定单元，则以树形查看单元。
* stop：停止单元。
* umask：取消禁用单元。

* --all：查看所有单元。
* --failed：查看执行失败的单元。
* --state STATE[,...]：查看指定状态的单元：可为active、failed、running、enabled。
* --type TYPE：查看指定类型的单元。可为service。

# 系统信息

## date

查看或设置时间。

```shell
date [-d TIME] [+FORMAT]
date -s TIME
```

* -d：指定时间。
* -s：设置系统时间。

FORMAT可使用如下格式：

* %%：%字面值。
* %a：星期几（Sun-Sat）。
* %A：星期几（Sunday-Saturday）。
* %b：月份（Jan-Dec）。
* %B：月份（January-December）。
* %c：本地日期时间。
* %C：世纪。
* %d：日（01-31）。
* %D：即“%m/%d/%y”。
* %e：即“%_d”。
* %F：即“%Y-%m-%d”。
* %g：该周所属ISO年份的最后两位数字，一月前几天可能属于上一年的一周（00-99）。
* %G：该周所属ISO年份，一月前几天可能属于上一年的一周（0000-9999）。
* %h：同%b。
* %H：小时（00-23）。
* %I：小时（01-12）。
* %j：一年中的第几天（001-366）。
* %k：小时（0-23）。
* %l：小时（1-12）。
* %m：月份（01-12）。
* %M：分钟（00-59）。
* %n：换行符。
* %N：纳秒（000000000-999999999）。
* %p：本地AM/PM。
* %P：本地am/pm。
* %r：即“%H:%M:%S %p”。
* %R：即“%H:%M”。
* %s：从1970-01-01 00:00:00 UTC到目前为止的秒数。
* %S：秒（00-60）。
* %t：制表符。
* %T：即“%H:%M:%S”。
* %u：一周中的第几天，以周一开始一周（1-7）。
* %U：一年中的第几周，以周日开始一周（00-53）。
* %V：一年中的第几周，以周一开始一周，以ISO规范计算（01-53）。
* %w：一周中的第几天，以周日开始一周（0-6）。
* %W：一年中的第几周，以周一开始一周（00-53）。
* %x：本地日期。
* %X：本地时间。
* %y：年份最后两位数字（00-99）。
* %Y：年份（0000-9999）。
* %z：+hhmm形式的数值时区。
* %:z：+hh:mm形式的数值时区。
* %::z：+hh:mm:ss形式的数值时区。
* %:::z：数值时区，只使用必要的“:”。
* %Z：字母时区。

TIME可使用如下格式：

* 2006-01-02 15:04:05

## dmesg

查看开机后产生的内核信息。

```shell
dmesg
```

## hostname

查看主机名。

```shell
hostname
```

## uname

查看内核信息。

```shell
uname [-a]
```

* -a：列出所有信息。

# 系统资源

top也可查看系统资源使用情况，其显示值与下面很多命令均一致。

## free

查看内存使用情况。

```shell
free [-bgkmt]
```

* -b：以B为单位。
* -g：以G为单位。
* -k：以K为单位，默认设置。
* -m：以M为单位。
* -t：显示物理内存和内存交互空间的总和。

free的结果与top一致。对某些发行版：total=used+free+buff/cache，available为程序可用的内存。对于某些发行版：total=used+free，used和free在Mem行表示系统已分配和未分配的内存，在-/+ buffers/cache行表示不计算缓存，程序已用和可用的内存。

## fuser

查找打开指定文件的进程。

```shell
fuser [-muv] FILENAME
```

* -m：如果是目录，则查找所有在该目录下使用文件的进程。
* -u：同时列出进程的所有者。
* -v：列出详细信息。

ACCESS字段的含义：

* c：进程在当前目录下（非子目录）。
* e：可被触发为执行状态。
* f：是一个被打开的文件。
* F：文件被打开了，不过在等待回应中。
* m：可能为动态库。
* r：代表顶层目录。

## getcap

查看可执行文件获取的内核权限。

```shell
getcap FILENAME
```

## iostat

查看CPU和I/O状态。

```shell
iostat [-x]
```

* -x：显示扩展的状态信息。

## lsof

列出被进程打开的文件。

```shell
lsof [-a -u USERNAME +d DIR -p PID -i:PORT]
```

* -a：列出多个选项同时成立的结果。
* -u USERNAME：列出该用户的进程打开的文件。
* +d DIR：列出指定目录下打开的文件。
* -i:PORT：列出使用指定端口的连接。
* -p PID：列出指定进程ID打开的文件。

## mpstat

查看CPU状态。使用/proc/stat中的信息。

```shell
mpstat [-P ALL]
```

* -P ALL：同时显示每个CPU核心的状态。

## sar

系统性能分析工具。

```shell
sar -n NET INTERVAL COUNT
```

* -A：所有报告的汇总。
* -b：报告缓冲区使用情况。
* -d：报告磁盘使用状态。
* -n NET：报告网络状态。NET可为ALL。
* -r：报告没有使用的内存页面和硬盘块。
* -u：报告CPU使用率。
* -v：报告进程、I节点、文件和锁表状态。
* -w：报告系统的交换活动。

## setcap

设置可执行文件的内核权限。

```shell
setcap CAPABILITY FILENAME
setcap -r FILENAME
```

* -r：清除权限。

CAPABILITY为`cap_net_bind_service=+eip`可让程序监听小于1024的端口。

## swapon

查看交换分区使用情况。

```shell
swapon -s
```

* -s：查看交换分区使用情况。

## ulimit

显示或设置当前shell的系统资源限制。

```shell
ulimit -a
ulimit [-HS -cdflntu [N]]
```

* -a：列出当前的所有限制。
* -c [N]：显示或设置内核文件大小限制。
* -d [N]：显示或设置进程段内存大小限制。
* -f [N]：显示或设置单个文件大小限制，单位为K。
* -H：严格的限制。
* -l [N]：显示或设置可用于锁定的内存大小限制。
* -n [N]：显示或设置打开的文件描述符数量限制。
* -S：宽松的限制。
* -t [N]：显示或设置CPU时间限制，单位为秒。
* -u [N]：显示或设置进程数量限制。

输出字段说明：

* max user processes：最大用户进程/线程数量。

## uptime

查看系统启动时间和工作负载。

```shell
uptime
```

依次显示：当前的时间，系统开机至今的时长，当前登录的用户数，最近1/5/15分钟的平均工作负载（即在特定的时间间隔内队列中运行的平均进程数）。

## vmstat

查看系统资源状态。

```shell
vmstat [interval [times]] [-a] [-S UNIT]
vmstat [interval [times]] -d
vmstat [interval [times]] -f
vmstat [interval [times]] -s [-S UNIT]
vmstat [interval [times]] -p PARTITIONDEV
```

* -a：将memory的buff、cache替换为inact、active。
* -d：列出磁盘的读写情况。
* -f：列出开机后fork的进程数。
* -s：列出开机后导致内存变化的情况。
* -S UNIT：指定显示的单位，可为k、K、m、M。
* -p PARTITIONDEV：列出分区的读写情况。

缺省参数时的输出字段说明：

* procs：进程。
	* r：等待和运行中的进程数量。
	* b：不可被唤醒的进程数量。
* memory：内存。
	* swpd：已使用的内存交换空间，同free的Swap行used列。
	* free：空闲的物理内存，同free的Mem行free列。
	* buff：缓存磁盘块设备的元数据，buff+cache同free的Mem行buff/cache列。
	* cache：缓存读写文件的页，buff+cache同free的Mem行buff/cache列。
* swap：内存交换空间。
	* si：每秒从内存交换空间换入的数量。
	* so：每秒换出至内存交换空间的数量。
* io：I/O。
	* bi：每秒从磁盘读取的块数。
	* bo：每秒写入到磁盘的块数。
* system：系统。
	* in：每秒被中断的进程次数。
	* cs：每秒上下文切换的次数。
* cpu：CPU使用率，与top的%CPU含义相同。

# 网络

## arp

ARP工具。

```shell
arp
```

如不指定该选项，则查看ARP缓存。

## ethtool

查看网络接口信息。

```shell
ethtool ETH
```

## firewall-cmd

设置防火墙。

```shell
firewall-cmd --zone=public --add-port=PORT/tcp --permanent
firewall-cmd --zone=public --remove-port=PORT/tcp --permanent
firewall-cmd --reload
firewall-cmd --list-ports
```

* --add-port：添加端口，值为：端口/协议。
* --list-ports：查看所有打开的端口。
* --permanent：设置为永久生效。
* --reload：重新加载防火墙规则，无需断开连接。防火墙规则修改后需重新加载才能生效。
* --remove-port：移除端口，值为：端口/协议。
* --zone：设置作用域。

## ifconfig

网络接口配置。

```shell
ifconfig [-a]
```

* -a：显示所有的网络接口，即使其是未激活的。

## ip

网络配置。RHEL系列使用其替代`ifconfig`。

```shell
ip addr
```

* addr：查看网络接口地址信息。

## nc

TCP/UDP网络工具。等同`netcat`。

```shell
nc -l [-tuv] -p PORT
nc [-tuv] HOST PORT
```

* -l：服务器模式。不使用此参数则为客户端模式，会要求输入传输内容。
* -p PORT：监听的端口。
* -t：使用TCP。如不指定-t和-u，则默认为TCP。
* -u：使用UDP。如不指定-t和-u，则默认为TCP。
* -v：显示详细信息。更详细的信息可使用-vv。

## netcat

TCP/UDP网络工具。等同`nc`。

## netstat

查看网络状态。

```shell
netstat [-alnpstu]
```

* -a：将所有连接、监听、套接字状态都列出。
* -l：列出监听状态。
* -n：列出端口号，不列出服务名。
* -p：列出进程信息。
* -s：列出每种协议的概要统计信息。
* -t：列出TCP连接状态，与-l配合可列出TCP监听状态。
* -u：列出UDP连接状态，与-l配合可列出UDP监听状态。

## mtr

网络连通性测试工具。

```shell
mtr [-r] HOST
```

* -r：使用报告模式。

## nmap

端口扫描工具。

```shell
nmap [-Av[v]] HOST
```

* -A：启用操作系统探测和版本探测。
* -v[v]：显示详细信息。

## ping

发送ICMP包。会不断重复发送。

```shell
ping HOST
```

## route

IP路由表工具。

```shell
route [-n]
```

* -n：显示数值IP。默认显示主机名。

## ss

查看网络状态。RHEL系列使用其替代`netstat`。

```shell
ss [-alnpstu]
```

* -a：将所有连接、监听、套接字状态都列出。
* -l：列出监听状态。
* -n：列出端口号，不列出服务名。
* -p：列出进程信息。
* -s：列出概要统计信息。
* -t：列出TCP连接状态，与-l配合可列出TCP监听状态。
* -u：列出UDP连接状态，与-l配合可列出UDP监听状态。

## tcpdump

网络抓包工具。

```shell
tcpdump [-n -i ETH -w FILENAME] [FILTER]
```

* -i ETH：指定网络接口。
* -n：显示IP而不是主机名。
* -w FILENAME：数据包写入的文件名。

FILTER过滤规则的示例如下：

* host IP
* dst IP and (port PORT1 or PORT2)
* dst IP and tcp and port PORT

## telnet

使用TCP连接远程主机的端口。

```shell
telnet HOST PORT
```

## traceroute

路由跟踪。

```shell
traceroute HOST
```

# HTTP

## ab

HTTP压力测试。

```shell
ab -c CONCURRENCY -n N URL
ab -c CONCURRENCY -n N -T CONTENTTYPE -p BODYFILENAME URL
```

* -c CONCURRENCY：并发数。
* -n N：请求总数。
* -p BODYFILENAME：将文件内容指定为请求实体。只用于POST请求。
* -T CONTENTTYPE：指定Content-Type首部。只用于POST请求。

URL不可省略路径。

## curl

发送HTTP请求。

```shell
curl URL [-iILv]
curl URL -X METHOD -H HEADER -d BODY [-iv]
```

* -d BODY：指定请求实体。同时默认-X为POST。
* -H HEADER：指定请求首部。格式为“KEY: VALUE”。
* -i：响应打印包含首部信息。
* -I：提交HEAD请求，只返回首部信息。
* -L：跟随HTTP重定向重新请求。
* -X METHOD：指定请求方法。默认为GET。
* -v：打印详细信息。

## wget

发送HTTP的GET请求下载文件。

```shell
wget [-O FILENAME] URL
```

* -O FILENAME：指定保存响应内容的文件名。

# 证书

## openssl

1. 生成CA私钥，期间会要求设置密码：

	```shell
	openssl genrsa -des3 -out CAKEYFILENAME
	```
1. 使用CA私钥生成CA证书，期间会要求输入CA私钥的密码：

	```shell
	openssl req -new -x509 -days N -key CAKEYFILENAME -out CACERTFILENAME
	```
1. 生成服务器私钥，期间会要求设置密码：

	```shell
	openssl genrsa -des3 -out SERVERKEYFILENAME
	```
1. 使用服务器私钥生成服务端CSR（证书签名请求），期间会要求输入服务器私钥的密码：

	```shell
	openssl req -new -key SERVERKEYFILENAME -out SERVERCSRFILENAME
	```

	输入的选项中最重要的是Common Name（即CN），要么是从DNS解析后的完全限定域名（fully qualified domain name/FQDN），要么是*即可在任何服务器使用。
1. 使用CA证书对服务端CSR进行签名生成服务器证书，期间会要求输入CA私钥的密码，并需存在文件内容为一个整数的ca.srl文件：

	```shell
	openssl x509 -req -days N -CA CACERTFILENAME -CAkey CAKEYFILENAME -in SERVERCSRFILENAME -out SERVERCERTFILENAME
	```
1. 清除服务器私钥的密码，便于服务器使用，期间会要求输入服务器私钥的密码：

	```shell
	openssl rsa -in SERVERKEYFILENAME -out SERVERKEYFILENAME
	```
1. 生成客户端私钥，期间会要求设置密码：

	```shell
	openssl genrsa -des3 -out CLIENTKEYFILENAME
	```
1. 使用客户端私钥生成客户端CSR（证书签名请求），期间会要求输入客户端私钥的密码：

	```shell
	openssl req -new -key CLIENTKEYFILENAME -out CLIENTCSRFILENAME
	```
1. 使用CA证书对客户端CSR进行签名生成客户端证书，期间会要求输入CA私钥的密码，并需存在文件内容为一个整数的ca.srl文件：

	```shell
	openssl x509 -req -days N -CA CACERTFILENAME -CAkey CAKEYFILENAME -in CLIENTCSRFILENAME -out CLIENTCERTFILENAME -extfile CLIENTEXTFILENAME
	```

	CLIENTEXTFILENAME用来添加扩展的SSL属性，格式为“KEY = VALUE”。
1. 清除客户端私钥的密码，便于客户端使用，期间会要求输入客户端私钥的密码：

	```shell
	openssl rsa -in CLIENTKEYFILENAME -out CLIENTKEYFILENAME
	```

# 软件安装

## apt

查询、管理APT包。

```shell
apt edit-sources
apt install APTPACKAGE
apt list APTPACKAGE|--installed
apt purge APTPACKAGE
apt remove APTPACKAGE
apt search PATTERN
apt show APTPACKAGE
apt update
apt upgrade
```

* --installed：用于列出所有已安装的apt包。

子命令：

* edit-sources：编辑apt源列表。
* install：安装apt包，同apt-get install。
* list：列出指定的apt包。
* purge：删除apt包，同时删除配置文件，同apt-get purge。
* remove：删除apt包，保留配置文件，同apt-get remove。
* search：从服务器搜索apt包，使用通配符匹配，同apt-cache search。
* show：查看apt包的信息，同apt-cache show。
* update：刷新本地apt存储库索引，其他子命令依赖此索引，同apt-get update。
* upgrade：更新所有已安装的apt包，同apt-get upgrade。

## apt-cache

查询APT包。

```shell
apt-cache depends APTPACKAGE
apt-cache rdepends APTPACKAGE
apt-cache search PATTERN
apt-cache show APTPACKAGE
```

子命令：

* depends：查看apt包依赖，即指定的apt包依赖哪些apt包。
* rdepends：查看apt包反向依赖，即指定的apt包被哪些apt包依赖。
* search：从服务器搜索apt包，使用通配符匹配。
* show：查看apt包的信息。

## apt-get

管理APT包。

```shell
apt-get autoclean
apt-get clean
apt-get install APTPACKAGE
apt-get purge APTPACKAGE
apt-get remove APTPACKAGE [--purge]
apt-get update [--allow-releaseinfo-change]
apt-get upgrade
```

* --purge：如使用该参数，则同时删除配置文件，否则不删除。
* --allow-releaseinfo-change：如`apt-get update`提示类似“N: Repository 'http://deb.debian.org/debian buster InRelease' changed its 'Suite' value from 'stable' to 'oldstable'”的错误，使用此选项来允许发布信息的改变。

子命令：

* autoclean：清理apt缓存。
* clean：清理apt缓存，比autoclean更彻底。
* install：安装apt包。
* purge：删除apt包，同时删除配置文件。同`remove --purge`。
* remove：删除apt包，保留配置文件。
* update：刷新本地apt存储库索引，其他子命令依赖此索引。
* upgrade：更新所有已安装的apt包。

## dpkg

使用DPKG管理包。

```shell
dpkg -i DEBFILENAME
dpkg -l
dpkg -L DEBPACKAGE
dpkg -P DEBPACKAGE
dpkg -r DEBPACKAGE
```

* -i：安装deb包。
* -l:显示所有已安装的deb包。
* -L：显示deb包安装于文件系统中的文件。
* -P：删除deb包，包括配置文件。
* -r：删除deb包，保留配置文件。

## rpm

使用RPM管理包。

RPM包文件名通常是如下形式：包名-版本信息-发布次数.硬件平台.rpm。

**RPM安装：**

```shell
rpm -i[hv] RPMFILENAME|URL[ ...]
```

* -h：显示进度条。
* -i：install，安装。
* -v：显示详细信息。

**RPM更新与升级：**

```shell
rpm -F[hv] RPMFILENAME[ ...]
rpm -U[hv] RPMFILENAME[ ...]
```

* -F：freshen，包已安装则更新。
* -U：upgrade，包已安装则更新，包未安装则安装。

**RPM查询：**

```shell
rpm -q[cdilR] RPMPACKAGE
rpm -qa
rpm -qf FILENAME
rpm -qp[cdilR] RPMFILENAME
```

* -c：列出包的所有配置文件。
* -d：列出包的所有帮助文件。
* -i：列出包的详细信息。
* -l：列出包的所有文件。
* -R：列出包依赖的其他文件。
* -q：查询已安装的包。
* -qa：查询所有已安装的包。
* -qf：查询文件所属的包。
* -qp：查询包文件的信息。

**RPM卸载：**

```shell
rpm -e RPMPACKAGE
```

* -e：erase，卸载包。

**RPM重建数据库：**

```shell
rpm --rebuilddb
```

* --rebuilddb：重建数据库。

**RPM安装数字证书：**

```shell
rpm --import KEYFILENAME|URL
```

** --import：安装数字证书。

## yum

使用YUM管理包。

```shell
yum info YUMPACKAGE
yum install YUMPACKAGE|RPMPACKAGE
yum list [YUMPACKAGE]
yum list installed [YUMPACKAGE]
yum list updates
yum makecache
yum provides FILENAME
yum remove YUMPACKAGE
yum repolist [all]
yum search PATTERN
yum update YUMPACKAGE
```

* -y：当需要用户输入时，自动提供yes响应。

子命令：

* info：从服务器查找软件信息。
* install：从服务器安装软件。
* list：从服务器列出所有软件，可指定软件名。
* list installed：列出已安装的软件，可指定软件名。
* list updates：从服务器查询已安装的软件中哪些可升级。
* makecache：将服务器的软件信息下载到本地缓存。
* provides：从服务器查询包含指定文件名的软件。
* remove：删除已安装的软件。
* repolist [all]：列出已安装的YUM源，如使用`all`则同时列出disabled状态的源。
* search：从服务器搜索软件，使用通配符匹配。
* update：从服务器升级软件。

# 程序和库

## ldconfig

配置共享库搜索。

```shell
ldconfig
ldconfig -v
```

如不使用选项，则根据/etc/ld.so.conf刷新共享库的搜索缓存。

* -v：显示每个搜索目录的详细信息。

## ldd

打印二进制文件依赖的共享库。

```shell
ldd FILENAME
```

## nm

查看符号表。

```shell
nm FILENAME
```

## readelf

查看ELF文件的信息。

```
readelf -a FILENAME
```

* -a：查看所有信息。

## 进程调试

## strace

查看进程的系统调用。

```shell
strace COMMAND [ARG[ ...]]
strace -p PID
```

* -p PID：指定进程ID。

# 远程连接

## scp

```shell
scp [-r -P PORT] SRC USERNAME@HOST:DST
scp [-r -P PORT] USERNAME@HOST:SRC DST
```

* -r：递归复制子目录和文件。
* -P PORT：指定远程SSH端口。

若SRC为文件，DST可为文件名或目录名；若SRC为目录，DST只能为目录名。

## ssh

```shell
ssh [-p PORT] USERNAME@HOST
```

* -p PORT：指定远程SSH端口。

# 计算

## base64

生成base64编码。

```shell
base64 FILENAME|STDIN
```

## md5sum

生成md5摘要。

```shell
md5sum FILENAME|STDIN
```

## sha256sum

生成sha256摘要。

```shell
sha256sum FILENAME|STDIN
```

# 变量

## declare

设置/查看环境变量。

```shell
declare [[-airx] VARIABLE[=VALUE]]
```

* -a：指定变量为数组。
* -i：指定变量为整数。
* -r：指定变量为只读。
* -x：指定变量为环境变量。

只使用`declare`时，查看所有环境变量，与`set`相同。

## env

查看所有环境变量。

```shell
env
```

## export

设置/查看环境变量。

```shell
export [VARIABLE[=VALUE]]
```

只使用`export`时，查看所有环境变量，显示格式包含"declare"。

## locale

查看语系变量。

```shell
locale [-a]
```

* -a：查看系统支持的所有语系。

只使用`locale`时，查看当前的语系变量。

## read

从标准输入读取一行至变量。

```shell
read [-p PROMPT -t TIMEOUT] VARIABLE STDIN
```

* -p PROMPT：指定提示符。
* -t TIMEOUT：指定等待超时，以秒为单位。

## set

查看所有变量，包括环境变量和自定义变量。

```shell
set
```

## typeset

设置/查看环境变量。用法与`declare`相同。

## unset

取消变量设置，可为环境变量或自定义变量。

```shell
unset VARIABLE
```

# 命令执行

## alias

设置命令别名。alias实际上是shell内置的命令。

```shell
alias NEW=OLD
```

## echo

打印回显。

```shell
echo [-e] CONTENT[ ...]
```

* -e：使用转义字符。

## exit

退出执行。

```shell
exit [CODE]
```

## history

查看历史执行命令。

```shell
history [N]
histroy -c
history -r FILENAME
history -aw [FILENAME]
```

如不使用选项，则列出所有历史命令。可指定列出最近N条命令。

* -a：将当前shell新增的历史命令追加入文件中。如没指定FILENAME，则默认为`~/.bash_history`。
* -c：将当前shell所有历史命令清除。
* -r：将FILENAME中的历史命令读到当前shell中。
* -w：将当前shell的历史命令写入文件中。如没指定FILENAME，则默认为`~/.bash_history`。

可通过如下方式执行历史命令：

* !N：执行第N条命令。
* !COMMAND：执行最近的以COMMAND开头的命令。
* !!：执行上一条命令。

## seq

返回数值序列。

```shell
seq [FIRST [INCREMENT]] LAST
```

FIRST和INCREMENT默认为1。返回序列的范围为[FIRST, LAST]。

## sh

在子进程中执行脚本。

```shell
sh [-nvx] SCRIPTFILENAME
sh [-nvx] -c SCRIPTSTR
```

* -c SCRIPTSTR：执行字符中的脚本。
* -n：不执行，只检查语法。
* -v：执行时，先打印脚本内容。
* -x：执行，并打印每一步使用的脚本。

## shift

将参数列表左移。

```shell
shift [N]
```

N默认为1。

## sleep

睡眠等待。

```shell
sleep SECONDS
```

## source

在父进程中执行脚本。

```shell
source SCRIPTFILENAME
```

等同于`. SCRIPTFILENAME`。

## tee

双向重定向。将标准输入重定向至文件和标准输出。

```shell
tee [-a] FILENAME[ ...]
```

* -a：以追加方式写入文件。

## trap

捕获信号后执行命令。

```shell
trap COMMAND SIGNAL
```

## unalias

取消命令别名。

```shell
unalias NAME
```

## xargs

生成命令的参数。

```shell
xargs COMMAND STDIN
```

参数以空白符分隔。
