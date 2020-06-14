本文更新于2020-05-03，使用MySQL 5.7，操作系统为Deepin 15.4。

常见的监控系统有：

* Cacti：使用SNMP采集数据，RRDtool绘图。
* Nagios：通常由一个主程序（Nagios）、一个插件程序（Nagios-plugins）和4个可选的组件（NRPE、NSCA、NSClient++、NDOUtils）组成。
* Zabbix：由Zabbix Server和可选组件Zabbix Agent组成。MPM是一套比较完整的监控MySQL的Zabbix插件。
