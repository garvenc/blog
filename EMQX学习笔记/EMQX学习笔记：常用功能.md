本文更新于2023-03-01，使用EMQX 4.4.3。

# 禁止匿名登录

1. 修改/etc/emqx/emqx.conf中相应的行如下：
	```
	allow_anonymous = false
	```
1. 重启EMQX。
	```shell
	emqx start
	```

# HTTP回调CONNECT身份验证

1. 配置鉴权回调URL。根据实际情况修改/etc/emqx/plugins/emqx_auth_http.conf中相应的行如下：
	```
	auth.http.auth_req.url = http://127.0.0.1:80/mqtt/auth
	```
	回调参数见文档：[https://www.emqx.io/docs/zh/v4.4/advanced/auth-http.html](https://www.emqx.io/docs/zh/v4.4/advanced/auth-http.html)。
1. 重新加载插件。
	```shell
	emqx_ctl plugins reload emqx_auth_http
	```

# HTTP回调发布/订阅ACL权限控制

1. 配置鉴权回调URL。根据实际情况修改/etc/emqx/plugins/emqx_auth_http.conf中相应的行如下：
	```
	auth.http.acl_req.url = http://127.0.0.1:80/mqtt/acl
	```
	回调参数见文档：[https://www.emqx.io/docs/zh/v4.4/advanced/acl-http.html](https://www.emqx.io/docs/zh/v4.4/advanced/acl-http.html)。
1. 重新加载插件。
	```shell
	emqx_ctl plugins reload emqx_auth_http
	```

# WebHook回调

1. 配置回调URL。根据实际情况修改/etc/emqx/plugins/emqx_web_hook.conf中相应的行如下：
	```
	web.hook.url = http://127.0.0.1:80
	
	#web.hook.rule.client.connect.1       = {"action": "on_client_connect"}
	#web.hook.rule.client.connack.1       = {"action": "on_client_connack"}
	#web.hook.rule.client.connected.1     = {"action": "on_client_connected"}
	#web.hook.rule.client.disconnected.1  = {"action": "on_client_disconnected"}
	#web.hook.rule.client.subscribe.1     = {"action": "on_client_subscribe"}
	#web.hook.rule.client.unsubscribe.1   = {"action": "on_client_unsubscribe"}
	#web.hook.rule.session.subscribed.1   = {"action": "on_session_subscribed"}
	#web.hook.rule.session.unsubscribed.1 = {"action": "on_session_unsubscribed"}
	#web.hook.rule.session.terminated.1   = {"action": "on_session_terminated"}
	#web.hook.rule.message.publish.1      = {"action": "on_message_publish"}
	#web.hook.rule.message.delivered.1    = {"action": "on_message_delivered"}
	#web.hook.rule.message.acked.1        = {"action": "on_message_acked"}
	```
	如需监听相应事件，将对应的`#`注释符去掉。
	
	事件参数见文档：[https://www.emqx.io/docs/zh/v4.4/advanced/webhook.html](https://www.emqx.io/docs/zh/v4.4/advanced/webhook.html)。
1. 重新加载插件。
	```shell
	emqx_ctl plugins reload emqx_web_hook
	```

# 加载插件

有以下几种方法，根据实际情况修改插件名：

* 加载插件：
	```shell
	emqx_ctl plugins load emqx_web_hook
	```
* 重新加载插件：
	```shell
	emqx_ctl plugins reload emqx_web_hook
	```
* 在/var/lib/emqx/loaded_plugins加入一行：
	```
	emqx_web_hook.
	```
* 在/var/lib/emqx/loaded_plugins加入一行：
	```
	{emqx_web_hook,true}.
	```
