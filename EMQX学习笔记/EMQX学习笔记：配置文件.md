本文更新于2023-02-27，使用EMQX 4.4.3。

[TOC]

官方文档：[https://www.emqx.io/docs/zh/v4.4/configuration/configuration.html](https://www.emqx.io/docs/zh/v4.4/configuration/configuration.html)

Linux下的配置文件目录为/etc/emqx，Windows下的配置文件目录为解压目录下的emqx/etc。

# acl.conf

# emqx.conf

EMQX主配置。

* acl_cache_max_size：每个Client缓存的最大ACL记录数。默认为32。
* acl_cache_ttl：ACL缓存的过期时间。默认为1m。
* acl_deny_action：ACL权限检验失败时的动作。ignore或disconnect，默认为ignore。
* acl_nomatch：查询不到ACL记录时是否对PUBLISH和SUBSCRIBE权限进行限制。默认为allow。
* allow_anonymous：是否允许匿名CONNECT。默认为true。
* broker.sys_interval：系统主题的发布时间间隔。默认为1m。
* enable_acl_cache：是否开启ACL缓存。默认为on。
* listener.ssl.external：External监听器的MQTTS端口。默认为8883。
* listener.ssl.external.certfile：External监听器的MQTTS端口的证书cert文件路径。默认为/etc/emqx/certs/cert.pem。
* listener.ssl.external.keyfile：External监听器的MQTTS端口的证书key文件路径。默认为/etc/emqx/certs/key.pem。
* listener.tcp.external：External监听器的MQTT端口。默认为0.0.0.0:1883。
* listener.tcp.internal：Internal监听器的MQTT端口。默认为127.0.0.1:11883。
* node.cookie：节点间的Cookie认证。集群中节点的Cookie需保持一致。默认为emqxsecretcookie。
* node.name：EMQX的Broker节点名。默认为emqx@127.0.0.1。
* module.subscription.1.qos：自动订阅的QOS。可自定义序数（“1”）。默认为0。
* module.subscription.1.topic：自动订阅的主题名。可自定义序数（“1”）。默认为connected/%c/%u。
* shared_subscription_strategy：共享订阅分发策略。random为所有共享订阅者随机选择分发，round_robin为按照共享订阅者订阅的顺序分发，sticky为分发给上次分发的订阅者，hash为根据ClientId进行分发。默认为random。
* zone.external.session_expiry_interval：External监听器的会话过期时间。默认为2h。
* zone.internal.allow_anonymous：Internal监听器是否允许匿名认证。默认为true。

# plugins/emqx_auth_http.conf

HTTP权限验证插件配置。

* auth.http.acl_req.url：PUBLISH和SUBSCRIBE的ACL验证请求URL。默认为http://127.0.0.1:80/mqtt/acl。
* auth.http.auth_req.url：CONNECT的身份验证请求URL。默认为http://127.0.0.1:80/mqtt/auth。

# plugins/emqx_auth_jwt.conf

JWT权限验证插件配置。

* auth.jwt.secret：JWT的Secret。默认为emqxsecret。
* auth.jwt.verify_claims：是否开启Claim认证。默认为off。
* auth.jwt.verify_claims.username：默认为%u，表示需要验证Claim中的username字段。

# plugins/emqx_auth_mongo.conf

MongoDB权限验证插件配置。

* auth.mongo.acl_query.collection。存放PUBLISH和SUBSCRIBE的ACL信息的集合名。集合中的文档需有此三个数组字段：publish、subscribe、pubsub。默认为mqtt_acl。
* auth.mongo.acl_query.selector：查询PUBLISH和SUBSCRIBE的ACL记录的条件。默认为username=%u。
* auth.mongo.auth_query.collection：用于CONNECT的身份验证的集合名。默认为mqtt_user。
* auth.mongo.auth_query.password_field：用于CONNECT的身份验证的密码字段名。默认为password。
* auth.mongo.auth_query.password_hash：用于CONNECT的身份验证的密码字段加密方式。默认为sha256。
* auth.mongo.auth_query.selector：用于CONNECT的身份验证的记录查询条件。默认为username=%u。
* auth.mongo.database：MongoDB数据库名。默认为mqtt。
* auth.mongo.server：MongoDB服务器地址。默认为127.0.0.1:27017。

# plugins/emqx_coap.conf

CoAP插件配置。

# plugins/emqx_management.conf

管理API插件。

# plugins/emqx_web_hook.conf

WebHook插件配置。

* web.hook.body.encoding_of_payload_field：HTTP实体编码。默认为plain。
* web.hook.rule.client.connack.1：服务端准备下发连接应答报文时的动作。默认为{"action": "on_client_connack"}。
* web.hook.rule.client.connect.1：服务端收到客户端的连接报文时的动作。默认为{"action": "on_client_connect"}。
* web.hook.rule.client.connected.1：客户端认证完成并成功接入系统后的动作。默认为{"action": "on_client_connected"}。
* web.hook.rule.client.disconnected.1：客户端连接层在准备关闭时的动作。默认为{"action": "on_client_disconnected"}。
* web.hook.rule.client.subscribe.1：收到订阅报文后，执行鉴权前的动作。默认为{"action": "on_client_subscribe"}。
* web.hook.rule.client.unsubscribe.1：收到取消订阅报文后的动作。默认为{"action": "on_client_unsubscribe"}。
* web.hook.rule.message.acked.1：服务端在收到客户端发回的消息ACK后的动作。默认为{"action": "on_message_acked"}。
* web.hook.rule.message.delivered.1：消息准备投递到客户端前的动作。默认为{"action": "on_message_delivered"}。
* web.hook.rule.message.publish.1：服务端在发布（路由）消息前的动作。默认为{"action": "on_message_publish"}。
* web.hook.rule.session.subscribed.1：完成订阅操作后的动作。默认为{"action": "on_session_subscribed"}。
* web.hook.rule.session.terminated.1：会话销毁后的动作。默认为{"action": "on_session_terminated"}。
* web.hook.rule.session.unsubscribed.1：完成取消订阅操作后的动作。默认为{"action": "on_session_unsubscribed"}。
* web.hook.url：WebHook回调URL。默认为http://127.0.0.1:80。
