本文更新于2023-03-01。

[TOC]

# 通配符

* *：0个至无穷多个任意字符。
* ?：1个任意字符。
* [AB]：枚举中任意一个字符。
* [A-B]：范围中任意一个字符。
* [\^AB]：任意一个不在枚举中的字符。
* [\^A-B]：任意一个不在范围中的字符。

# 特殊符号

* \#：注释符。该行其后的内容均视为注释。
* \#!：声明使用的shell，于第一行使用，后面不带空格。如：`#!/bin/bash`。
* \\：转义符，并可将命令扩展至下一行。
* |：管道符。将左侧命令的标准输出作为右侧命令的标准输入。右侧命令可以使用`-`来指代标准输入。
* ||：左侧命令执行不成功，则执行右侧命令。否则不执行右侧命令。
* ;：连续命令分隔符。
* ~：用户主目录。
* $：使用变量前导符。
* &：作业控制符。将命令变为在后台工作。
* &&：左侧命令执行成功，则执行右侧命令。否则不执行右侧命令。
* !：逻辑非。
* /：目录路径分隔符。
* \>：输出流重定向，会替换内容。`>`和`1>`为重定向标准输出，`2>`为重定向标准错误输出，`&>`为重定向标准输出和标准错误输出。
* \>>：输出流重定向，会追加内容。`>>`和`1>>`为重定向标准输出，`2>>`为重定向标准错误输出，`&>>`为重定向标准输出和标准错误输出。
* <：输入流重定向。`<`和`0<`为重定向标准输入。
* <<：输入流重定向的结束字符串。
* ''：字符串。不能对$进行变量替换。
* ""：字符串。能对$进行变量替换。
* \`\`：取执行命令内容。同`$()`。
* ()：子shell的开始与结束。
* {}：命令块组合。
* []：检测文件和比较值。同`test`命令。
* [[]]：类似`[]`，但中间可使用`&&`、`||`连接多个条件，使用`==`作glob模式匹配，使用`=~`作正则表达式匹配。

# 变量

变量的设置规则如下：

* 变量名与变量值以`=`连接，`=`两侧不能有空格。
* 变量名只能是英文字母和数字，但不能以数字开头。
* 可使用转义字符`\`。
* 变量值如有空格可使用`""`或`''`引起，但`""`引起的特殊字符（如`$`）会使用其对应的值，而`''`引起的特殊字符仅为纯文本。可引起多行字符串。
* 变量如需使用其他变量的值，可使用`$NAME`或`${NAME}`（建议）。可使用`${NAME:START}`取START（含，从0开始）起的子串。可使用`${NAME:START:LENGTH}`取START起长度为LENGTH的子串。可使用`${NAME[INDEX]}`使用数组元素的值。
* 变量值如需使用其他命令执行的值，可使用\`COMMAND\`或`$(COMMAND)`（建议）。
* 变量值如需使用数值运算的值，可使用`$((CALCULATION))`或`$[CALCULATION]`。
* 如变量需在子进程使用，则需使用`export`将变量设置为环境变量。
* 变量类型默认为字符串。
* 通常大写变量为环境变量。

## 环境变量

* HISTSIZE：`history`历史记录的最大条数。
* HOME：用户主目录。
* HOSTNAME：主机名。
* HOSTTYPE：主机硬件类型。如：x86_64。
* LANG：语系。
* MACHTYPE：机器内核类型。如：x86_64-redhat-linux-gnu。
* MAIL：`mail`读取的邮箱文件。
* OSTYPE：操作系统类型。如：linux-gnu。
* PATH：可执行文件的查找路径。以`:`分隔顺序查找。
* PS1：命令提示符。
* RANDOM：随机变量。
* SHELL：shell可执行文件路径。
* TZ：时区。如：Asia/Shanghai。
* USER：用户名。

## 默认变量

为便于阅读，此小节的变量加上`$`变量前导符。

* $-：`set`的设置。
* $$：当前shell的PID。
* $?：上一个命令的返回码。通常执行成功返回0。
* $\#：去掉第0个命令行参数或函数参数的其余参数个数。
* $*：去掉第0个命令行参数或函数参数的其余参数，作为一个值。
* $@：去掉第0个命令行参数或函数参数的其余参数，作为多个值。
* $0,$1,...,$N：第N个命令行参数或函数参数。第0个参数为shell脚本文件名。
* ${!PARAM}：$PARAM的值作为下标的命令行参数或函数参数。

# 流程控制语句

## break

```shell
while CONDITION
do
	DO
	break
done
```

可用于所有循环语句。

## case

```shell
case $VAR in
	VALUE1)
		DO1
		;;
	VALUE2|VALUE3 | VALUE4 )
		DO2 ;;
	VALUE5)
		DO3;;
	*)
		;;
esac
```

## continue

```shell
while CONDITION
do
	DO
	continue
done
```

可用于所有循环语句。

## if

```shell
if CONDITION1; then
	DO1
elif CONDITION2; then
	DO2
else
	DO3
fi
```

CONDITION可为条件判断式`[ OPTEST ]`或使用`&&`、`||`连接的多个条件判断式。下同。

## for

```shell
for ((INIT;CONDITION;EACH))
do
	DO
done
```

`((`和`))`之间各值可以使用空格隔开，其中也可以使用`i++`、`i=i+1`这种表达式。

```shell
for VAR in VALUE1 VALUE2
do
	DO
done
```

VALUE*序列可为命令执行结果。

## until

当条件为真时，退出循环。

```shell
until CONDITION
do
	DO
done
```

## while

当条件不为真时，退出循环。

```shell
while CONDITION
do
	DO
done
```

## function

函数必须先定义，再调用。

函数定义：

```shell
function FUNC () {
	DO
}
```

函数调用：

```shell
FUNC PARAM1 PARAM2
```

# 示例

若干重定向示例：

```shell
cat input.txt > output.txt          # 以替换方式重定向标准输出。
cat input.txt 1> output.txt         # 以替换方式重定向标准输出。
cat input.txt 1> output.txt 2>&1    # 以替换方式重定向标准输出，同时将标准错误输出重定向至标准输出，即重定向标准输出和标准错误输出。
cat input.txt &> output.txt         # 以替换方式重定向标准输出和标准错误输出。
cat input.txt >> output.txt         # 以追加方式重定向标准输出。
cat < input.txt                     # 重定向标准输入。
cat 0< input.txt                    # 重定向标准输入。
cat << END                          # 标准输入当读取到内容为END的行时结束。
cat > output.txt < input.txt        # 重定向标准输入和标准输出。
```

从input.txt读取文件内容，并打印每一行：

```shell
while read line
do
	echo $line
done < input.txt
```
