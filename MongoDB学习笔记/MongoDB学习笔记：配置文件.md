本文更新于2024-02-02。使用MongoDB 6.0.4。

官方文档：[https://www.mongodb.com/docs/manual/reference/configuration-options/](https://www.mongodb.com/docs/manual/reference/configuration-options/)

Linux下配置文件为/etc/mongod.conf。

`#`后面的内容作为注释忽略。大多数参数与mongod的命令行参数对应，如命令行参数为不需指定值的开关则设置为`true`或`false`。

配置文件的内容如下：

* net：网络配置。
	* port：监听端口。非分片节点和配置服务器节点的mongod，以及mongos，默认为27017。分片节点的mongod默认为27018。配置服务器节点的mongod默认为27019。
	* bindIp：绑定的IP。以,分隔的列表，0.0.0.0为所有IPv4地址。默认为localhost。
* replication：副本集配置。
	* replSetName：副本集名称。
* security：安全配置。
	* authorization：是否启用权限验证。为enabled或disabled（默认）。
	* keyFile：密钥文件路径。当分片或副本集启用权限验证时必需指定。文件内容为6~1024个base64字符集的字符，所有者必需为mongodb，权限必需小于600。
* storage：存储配置。
	* dbPath：数据文件目录路径。默认为/data/db（各发行版会自定义默认值，如/var/lib/mongodb）。
	* directoryPerDB：是否每个数据库使用一个目录。默认为false。
* systemLog：日志配置。
	* path：日志文件路径。（Linux发行版的包会在初始化脚本中自定义默认值，如/var/log/mongodb/mongod.log，此情况下最好不要修改）
