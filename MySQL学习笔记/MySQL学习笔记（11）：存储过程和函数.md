本文更新于2019-06-23，使用MySQL 5.7，操作系统为Deepin 15.4。

[TOC]

# SQL语句

## 创建存储过程或函数

创建存储过程：

```sql
CREATE PROCEDURE name ({[IN|OUT|INOUT] param type}[, ...])
[characteristic]
body
```

创建函数：

```sql
CREATE FUNCTION name ({param type}[, ...]) RETURNS type
[characteristic]
body
```

type表示数据类型。

characteristic表示特征值，特征值目前只是提供信息给服务器，定义如下：

```sql
LANGUAGE SQL
|[NOT] DETERMINISTIC
|{CONTAINS SQL|NO SQL|READS SQL DATA|MODIFIES SQL DATA}
|SQL SECURITY {DEFINER|INVOKER}
|COMMENT 'string'
```

* LANGUAGE SQL：说明body是使用SQL编写的。该值是默认的。
* [NOT] DETERMINISTIC：说明是确定的，即是否同样的输入产生同样的输出。默认为`NOT DETERMINISTIC`。
* CONTAINS SQL|NO SQL|READS SQL DATA|MODIFIES SQL DATA：目前并没有根据此特征值约束存储过程实际对数据的使用。`CONTAINS SQL`表示不包含读写语句；`NO SQL`表示不包含SQL语句；`READS SQL DATA`表示包含读语句不包含写语句；`MODIFIES SQL DATA`表示包含写语句。默认为`CONTAINS SQL`。
* SQL SECURITY {DEFINER|INVOKER}：表示使用创建者的权限来执行，还是使用调用者的权限来执行。默认为`DEFINER`。
* COMMENT 'string'：注释。

body由`BEGIN`和`END`包起，其中可以定义变量、条件、处理、游标，以及使用流程控制语句跳转逻辑。

存储过程和函数的`CREATE`语法不支持`OR REPLACE`。

存储过程和函数的区别：函数一定有返回值，存储过程一定没有返回值；函数的参数只能使用`IN`类型，存储过程的参数可使用`IN`、`OUT`、`INOUT`类型。

MySQL的存储过程和函数中允许包含DDL语句，允许执行提交或回滚，允许调用其他存储过程或函数，但是是不允许执行`LOAD DATA INFILE`语句。

通常在执行创建存储过程或函数前，使用`DELIMITER ;;`命令将语句的结束符从`;`修改成其他符号（示例使用`;;`）。在创建完毕后，通过`DELIMITER ;`命令将结束符还原。这样可避免存储过程定义中的`;`被错误解析为结束符。

## 修改存储过程或函数

```sql
ALTER PROCEDURE|FUNCTION name
[characteristic]
```

characteristic特征值如下：

```sql
{CONTAINS SQL|NO SQL|READS SQL DATA|MODIFIES SQL DATA}
|SQL SECURITY {DEFINER|INVOKER}
|COMMENT 'string'
```

## 删除存储过程或函数

```sql
DROP PROCEDURE|FUNCTION [IF EXISTS] name
```

一次只能删除一个存储过程或函数。

## 调用存储过程或函数

```sql
CALL name(param[, ...])
```

如果是输出参数，需传入变量，如：`@xxx`。

# 变量、条件、处理、游标

变量、条件、处理、游标都通过`DECLARE`定义，但它们之间是有先后顺序的：变量和条件必须在最前面声明，然后才是游标的声明，最后才是处理的声明。

## 变量

变量不区分大小写，作用范围只能在`BEGIN ... END`块中，可以用在嵌套的块中。变量的定义必须写在复合语句的开头，并且在其他语句的前面。

```sql
DECLARE var[, ...] type [DEFAULT value]
```

变量直接赋值：

```sql
SET {var = value}[, ...]
```

变量通过查询赋值，这要求查询返回的结果只有一行：

```sql
SELECT colname[, ...] INTO var[, ...] FROM ...
```

`FROM`后面的子句同普通的`SELECT`查询，在此省略。

## 条件

```sql
DECLARE condition_name CONDITION FOR
{SQLSTATE [VALUE] value}|mysql_error_code
```

条件枚的含义如下：

* `SQLSTATE [VALUE] value`：SQLSTATE代码，为一个字符串。
* `mysql_error_code`：mysql_error_code值，为一个整数。

## 处理

```sql
DECLARE CONTINUE|EXIT|UNDO HANDLER FOR
{SQLSTATE [VALUE] value|condition_name|SQLWARNING|NOT FOUND|SQLEXCEPTION|mysql_error_code}[, ...]
sp_statement
```

处理类型枚举值的含义：

* `CONTINUE`：继续执行后面的语句。
* `EXIT`：执行终止。
* `UNDO`：前面已执行的语句撤销，目前还不支持。

条件枚的含义如下：

* `SQLSTATE [VALUE] value`：SQLSTATE代码，为一个字符串。
* `condition_name`：`DECLARE`定义的`CONDITION`名。
* `SQLWARNING`：所有以01开头的SQLSTATE代码的速记。
* `NOT FOUND`：所有以02开头的SQLSTATE代码的速记。
* `SQLEXCEPTION`：所有没有被`SQLWARNING`和`NOT FOUND`捕获的SQLSTATE代码的速记。
* `mysql_error_code`：mysql_error_code值，为一个整数。

## 游标

声明游标：

```sql
DECLARE cursor_name CURSOR FOR select_statement
```

打开游标：

```sql
OPEN cursor_name
```

读取游标：

```sql
FETCH cursor_name INTO var[, ...]
```

关闭游标：

```sql
CLOSE cursor_name
```

# 流程控制

## IF

```sql
IF condition1 THEN statement_list1
[ELSEIF condition2 THEN statement_list2]
[...]
[ELSE statement_list3]
END IF
```

## CASE

```sql
CASE case_value
WHEN value1 THEN statement_list1
[...]
[ELSE statement_list2]
END CASE
```

或：

```sql
CASE
WHEN condition1 THEN statement_list1
[...]
[ELSE statement_list2]
END CASE
```

## LEAVE

从标注的流程中退出，通常和`BEGIN ... END`或循环一起使用。

```sql
LEAVE label
```

## ITERATE

`ITERATE`必须用在循环中，作用是跳过当前循环的剩下语句，进入下一轮循环。

```sql
ITERATE label
```

## LOOP

通常需在statement_list中指定退出条件，否则为死循环。

```sql
[begin_label:] LOOP
	statement_list
END LOOP [end_label]
```

## WHILE

当满足条件时执行循环。会在首次循环执行前判断条件，故循环最少执行0次。

```sql
[begin_label:] WHILE condition DO
statement_list
END WHILE [end_label]
```

## REPEAT

当满足条件时退出循环。会在首次循环执行后判断条件，故循环最少执行1次。

```sql
[begin_label:] REPEAT
	statement_list
UNTIL condition
END REPEAT [end_label]
```

# 示例

存储过程定义示例如下：

```sql
DELIMITER ;;

CREATE PROCEDURE sp_test(IN id INT, OUT sum INT)
BEGIN
	DECLARE var_value, var_sum INT DEFAULT 0;
	DECLARE flag INT DEFAULT 1;
	DECLARE cur CURSOR FOR SELECT value FROM t WHERE tid = id;
	DECLARE CONTINUE HANDLER FOR NOT FOUND SET flag = 0;
	OPEN cur;
	loop_cur: LOOP
		FETCH cur INTO var_value;
		IF flag = 0 THEN
			LEAVE loop_cur;
		END IF;
		var_sum = var_sum + var_value;
	END LOOP;
	CLOSE cur;
END;;

DELIMITER ;
```
