本文更新于2020-05-03，使用MySQL 5.7，操作系统为Deepin 15.4。

[TOC]

# 权限

权限存取需要用到mysql库中user、db、host、tables_priv、columns_prvi这几个权限表。列分为4个部分：用户列、权限列、安全列、资源控制列。权限列又分为普通权限和管理权限。

| 表名      | user                  | db                    | host                  | 说明           |
| -------- | --------------------- | --------------------- | --------------------- | -------------- |
| 用户列    | host                  | host                  | host                  |                |
| 用户列    | user                  | db                    | db                    |                |
| 用户列    | password              | user                  |                       |                |
| 权限列    | select_priv           | select_priv           | select_priv           | 查询记录         |
| 权限列    | insert_priv           | insert_priv           | insert_priv           | 插入记录         |
| 权限列    | update_priv           | insert_priv           | insert_priv           | 更新记录         |
| 权限列    | delete_priv           | delete_priv           | delete_priv           | 删除记录         |
| 权限列    | index_priv            | index_priv            | index_priv            | 维护索引         |
| 权限列    | alter_priv            | alter_priv            | alter_priv            | 修改数据库或表    |
| 权限列    | create_priv           | create_priv           | create_priv           | 创建数据库或表    |
| 权限列    | drop_priv             | drop_priv             | drop_priv             | 删除数据库或表    |
| 权限列    | grant_priv            | grant_priv            | grant_priv            | 授予权限         |
| 权限列    | create_view_priv      | create_view_priv      | create_view_priv      | 创建视图         |
| 权限列    | show_view_priv        | show_view_priv        | show_view_priv        | 展示视图         |
| 权限列    | create_routine_priv   | create_routine_priv   |                       | 创建存储过程或函数 |
| 权限列    | alter_routine_priv    | alter_routine_priv    |                       | 修改存储过程或函数 |
| 权限列    | references_priv       | references_priv       | references_priv       | ?               |
| 权限列    | reload_priv           |                       |                       | 重新加载MySQL    |
| 权限列    | shutdown_priv         |                       |                       | 关闭MySQL        |
| 权限列    | process_priv          |                       |                       | 查看所有线程      |
| 权限列    | file_priv             |                       |                       | 导出导入文件      |
| 权限列    | show_db_priv          |                       |                       | 展示所有库        |
| 权限列    | super_priv            |                       |                       | 超级权限          |
| 权限列    | create_tmp_table_priv | create_tmp_table_priv | create_tmp_table_priv | 创建临时表        |
| 权限列    | lock_tables_priv      | lock_tables_priv      | lock_tables_priv      | 锁表             |
| 权限列    | execute_priv          |                       |                       | 执行存储过程或函数  |
| 权限列    | repl_slave_priv       |                       |                       | 与复制有关         |
| 权限列    | repl_client_priv      |                       |                       | 与复制有关         |
| 安全列    | ssl_type              |                       |                       |                  |
| 安全列    | ssl_cipher            |                       |                       |                  |
| 安全列    | x509_issuer           |                       |                       |                  |
| 安全列    | x509_subject          |                       |                       |                  |
| 资源控制列 | max_questions         |                       |                       |                  |
| 资源控制列 | max_updates           |                       |                       |                  |
| 资源控制列 | max_connections       |                       |                       |                  |
| 资源控制列 | max_user_connections  |                       |                       |                  |

权限的存取有以下两个阶段：

1. 先从user表的host、user和password字段判断是否通过身份验证。
1. 按照以下权限表的顺序查看是否取得权限：user->db->tables_priv->columns_priv，如果有权限表相应的值为`Y`，则取得该权限。在这几个权限表中，权限范围依次递减，全局权限覆盖局部权限。

# 安全

## 操作系统相关的安全问题

* 严格控制操作系统账号（通常为mysql）和权限。
* 尽量避免以root（通常使用mysql）权限运行MySQL。 
* 防止DNS欺骗：创建账号时host可指定域名，但若域名对应的IP地址被恶意修改，则数据库就会被恶意的IP地址访问。

## 数据库相关的安全问题

* 删除匿名账号。
* 给root账号设置密码。
* 设置安全密码。使用密码有以下几种方式，也需注意安全问题：直接将密码写在命令行中；交互式输入密码；将账号和密码写在配置文件中，如写在/etc/my.cnf的client组中。
* 只授予账号必需的权限。
* 除root外，任何账号不应有mysql库user表的存取权限。
* 不要把`FILE`、`PROCESS`、`SUPER`权限授予管理员以外的账号。
* 注意`LOAD DATA LOCAL`从客户端本地读取文件带来的安全问题。
* 使用MERGE存储引擎潜藏的安全漏洞：通过MERGE表还可以访问已被回收权限的子表。
* `DROP TABLE`并不回收相关权限，导致重新创建同名表时，此前其他用户对此表的权限会自动赋予。
* 使用SSL。
* 如果可能，给所有用户加上访问host限制。
* `REVOKE`的漏洞：在一个数据库上多次赋予权限，权限会自动合并；但是在多个数据库上多次赋予权限，每个数据库上都会认为是单独的一组权限，必须在此数据库上用`REVOKE`单独进行权限回收，而`REVOKE ALL ON *.*`并不会自动完成这个过程。

## 数据库安全设置选项

* --old-passwords：该参数只是为了支持4.1版本前的客户端，这将使得新建或修改的密码全部变成旧格式，降低了系统安全性。
* --safe-user-create：使用户不能用`GRANT`创建新账号，除非用户有mysql.user表的`INSERT`权限。
* --secure-auth：使4.1版本前的客户端无法进行用户认证，即使使用了--old-passwords也不行。
* --skip-grant-tables：令服务器不使用权限系统。
* --skip-networking：不允许TCP/IP连接，所有连接必须经命名管道（Named Pipes）、共享内存（Shared Memory）、UNIX套接字（Socket）文件进行。
* --skip-show-database：使用该选项，只有赋予`SHOW DATABASES`权限的用户才能执行且显示所有库名；不使用该选项，则所有用户都能执行，但只显示有权限的库名。
