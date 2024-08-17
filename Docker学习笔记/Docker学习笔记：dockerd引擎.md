本文更新于2024-08-14，使用Docker 27.1.2，操作系统为Debian 12.6 (bookworm)。

[TOC]

**说明：下文中，大写为自定义变量，根据实际情况填写。使用`[]`引起表示内容可选，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

# dockerd服务

官方文档参阅：[https://docs.docker.com/engine/reference/commandline/dockerd/](https://docs.docker.com/engine/reference/commandline/dockerd/)

可使用以下选项：

* -D：以调试模式启动。
* -H unix://SOCKFILE|tcp://HOST[:PORT]|fd://[FDNUMBER]：监听地址，可使用多次。默认为unix:///var/run/docker.sock。tcp端口2375为非加密端口，2376为加密端口，默认使用2375。
* -s STORAGEDRIVER：存储驱动。可为aufs、devicemapper、btrfs、zfs、overlay、overlay2。
* --add-runtime NAME=PATH：注册OCI（Open Container Initiative）运行时。
* --config-file FILENAME：配置文件，格式为JSON，默认为/etc/docker/daemon.json。
* --containerd SOCKFILE：containerd的连接地址。
* --dns IP：容器的DNS服务器。
* --dns-search DOMAIN：容器的DNS搜索域。
* --icc：只允许内部容器通讯，禁止没有链接的容器间通讯。
* --insecure-registry HOST[:PORT]|IP/MASK：允许访问不安全的注册服务器，可使用多次。
* --tls：启动TLS但不进行客户端认证。
* --tlscacert FILENAME：CA证书文件名。
* --tlscert FILENAME：服务器证书文件名。
* --tlskey FILENAME：服务器私钥文件名。
* --tlsverify：启用TLS并进行客户端认证。

# 目录和文件

* /etc/docker/daemon.json：配置文件。
* /var/lib/docker/：保存Docker镜像、容器、容器配置。
	* containers/：保存容器。
		* 容器ID
			* 容器ID-json.log：JSON日志。可删除。
	* image/：保存镜像。
	* volumes/：保存卷。
* /var/run/docker.sock：绑定的Unix套接字。

## 系统服务配置文件

* 对于Ubuntu或Debian系统：/etc/default/docker
* 对于Read Hat或Fedora系统：/etc/sysconfig/docker
* 对于使用Upstart的系统：/etc/init/docker.conf
* 对于使用Systemd的系统：/usr/lib/systemd/system/docker.service

# 配置文件

/etc/docker/daemon.json配置文件的示例如下：

```json
{
	"api-cors-header": "",
	"authorization-plugins": [],
	"bip": "",
	"bridge": "",
	"cgroup-parent": "",
	"cluster-store": "",
	"cluster-store-opts": {},
	"cluster-advertise": "",
	"debug": true,
	"default-gateway": "",
	"default-gateway-v6": "",
	"default-runtime": "runc",
	"default-ulimits":{},
	"disable-legacy-registry": false,
	"dns": [],
	"dns-opts": [],
	"dns-search": [],
	"exec-opts": [],
	"exec-root": "",
	"fixed-cidr": "",
	"fixed-cidr-v6": "",
	"graph": "",
	"group": "",
	"hosts": "",
	"icc": false,
	"insecure-registries": [],
	"ip": "0.0.0.0",
	"iptables": false,
	"ipv6": false,
	"ip-forward": false,
	"ip-masq": false,
	"labels": [],
	"live-restore": true,
	"log-driver": "",
	"log-level": "",
	"log-opts": {},
	"max-concurrent-downloads": 3,
	"max-concurrent-uploads": 5,
	"mtu": 0,
	"oom-score-adjust": -500,
	"pidfile": "",
	"raw-logs": false,
	"registry-mirrors": [],
	"runtimes": {
		"runc": {
			"path": "runc"
		},
		"custom": {
			"path": "/usr/local/bin/my-runc-replacement",
			"runtimeArgs": [
				"--debug"
			]
		}
	},
	"selinux-enabled": false,
	"storage-driver": "",
	"storage-opts": [],
	"swarm-default-advertise-addr": "",
	"tls": true,
	"tlscacert": "",
	"tlscert": "",
	"tlskey": "",
	"tlsverify": true,
	"userland-proxy": false,
	"userns-remap": ""
}
```

* registry-mirrors：镜像的注册服务器地址列表。默认为https://hub.docker.com。
