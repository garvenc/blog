本文更新于2024-08-15，使用systemd 252，操作系统为Debian 12.6 (bookworm)。

以为Nginx编写配置文件为例，配置文件路径为/lib/systemd/system/nginxd.service（亦即服务名为nginxd），所有者为root，权限通常为0644。文件内容如下：

```
[Unit]
Description=Nginx
After=network.target
 
[Service]
Type=forking
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s quit
Restart=on-failure
 
[Install]
WantedBy=multi-user.target
```

* Unit
	* After：在此依赖列表之后启动。以空格分隔。
	* Before：在此依赖列表之前启动。以空格分隔。
	* Description：描述文本。
	* Documentation：文档地址。
	* Wants：弱依赖列表，即使其启动失败也不会对本单元有影响。以空格分隔。
* Service
	* Environment：环境变量列表。
	* ExecReload：重载指令。必需使用绝对路径。
	* ExecStart：启动指令。必需使用绝对路径。
	* ExecStartPost：启动后处理指令。必需使用绝对路径。
	* ExecStartPre：启动预处理指令。必需使用绝对路径。
	* ExecStop：停止指令。必需使用绝对路径。
	* KillMode：杀死进程的模式。control-group（默认）为杀死所有进程，包括子进程；process为只杀死主进程；mixed为主进程将收到SIGTERM信号，子进程收到SIGKILL信号；none为不杀死进程，只执行stop指令。
	* PrivateTmp：是否分配独立的临时空间。
	* Restart：重启策略。no（默认）为不重启；always为总是重启；on-success为只在成功退出（即返回码为0）时重启；on-failure为只在失败退出（即返回码非0）时重启。
	* RestartSec：重启时间间隔。默认为100ms。
	* TimeoutSec：停止的超时时间，超过则强制停止。
	* Type：启动类型。为forking时从父进程创建子进程，然后父进程退出；为simple（默认）时启动指令即为主体程序。
* Install
	* WantedBy：被外部依赖的列表。以空格分隔。
