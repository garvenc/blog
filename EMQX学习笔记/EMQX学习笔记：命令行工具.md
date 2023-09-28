本文更新于2023-02-28，使用EMQX 4.4.3。

[TOC]

# emqx

官方文档：[https://www.emqx.io/docs/zh/v4.4/getting-started/command-line.html](https://www.emqx.io/docs/zh/v4.4/getting-started/command-line.html)

* emqx console：控制台模式。
* emqx restart：重启EMQX。
* emqx start：启动EMQX。

# emqx_ctl

官方文档：[https://www.emqx.io/docs/zh/v4.4/advanced/cli.html](https://www.emqx.io/docs/zh/v4.4/advanced/cli.html)

* emqx_ctl admins：管理员管理。
* emqx_ctl admins passwd USERNAME PASSWORD：重置管理员密码。
* emqx_ctl cluster join OTHERNODE：加入集群。
* emqx_ctl cluster leave：当前节点退出集群。
* emqx_ctl cluster force-leave OTHERNODE：令其它节点退出集群。
* emqx_ctl mgmt：应用程序管理。
* emqx_ctl mgmt insert APPID NAME：创建应用程序账号。会返回AppSecret。
* emqx_ctl plugins load PLUGIN：加载插件。插件名称可参见`emqx_ctl plugins list`。
* emqx_ctl plugins list：列出所有插件。第一列为插件名，已加载的插件会显示active=true。
* emqx_ctl plugins reload PLUGIN：重新加载插件，未加载的插件会进行加载。插件名称可参见`emqx_ctl plugins list`。
* emqx_ctl plugins unload PLUGIN：卸载插件。插件名称可参见`emqx_ctl plugins list`。
