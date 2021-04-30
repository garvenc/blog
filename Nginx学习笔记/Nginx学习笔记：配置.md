本文更新于2021-04-30，使用nginx 1.16。

[TOC]

# 变量

配置中可使用以下变量：

* $N：正则表达式匹配时与第N个分组（以“()”引起）匹配的内容，从0开始。
* $http_upgrade：Upgrade首部的值。
* $request_uri：从路径开始的请求URI。

# 配置

`[]`引起表示可选，大写字母需使用实际的配置值。

## http

HTTP。

```
http {
}
```

## http.client_max_body_size

HTTP最大的实体大小。可使用k、m、g等表示大小。

```
http {
	client_max_body_size SIZE;
}
```

## http.server

HTTP服务。可指定多个。

```
http {
	server {
	}
}
```

## http.server.client_max_body_size

HTTP服务最大的实体大小。可使用k、m、g等表示大小。

```
http {
	server {
		client_max_body_size SIZE;
	}
}
```

## http.server.listen

HTTP服务监听的端口。可指定使用HTTPS（SSL）。

```
http {
	server {
		listen PORT [ssl];
	}
}
```

## http.server.location

URL路径前缀匹配规则。

```
http {
	server {
		location PATH_PATTERN {
		}
	}
}
```

## http.server.location.client_max_body_size

URL路径最大的实体大小。可使用k、m、g等表示大小。

```
http {
	server {
		location PATH_PATTERN {
			client_max_body_size SIZE;
		}
	}
}
```

## http.server.location.proxy_pass

反向代理请求的后端地址。

```
http {
	server {
		location PATH_PATTERN {
			proxy_pass PROXY_URL;
		}
	}
}
```

PROXY_URL中可使用`$request_uri`。

或，假设原始请求的路径为/PATH/SUB_PATH，PATH_PATTERN匹配/PATH。若PROXY_URL以/结尾，则代理请求的路径为PROXY_URL/SUB_PATH；若PROXY_URL不以/结尾，则代理请求的路径为PROXY_URL/PATH/SUB_PATH。

## http.server.location.proxy_read_timeout

反向代理从后端的读超时。可使用h、m、s等表示时长。

```
http {
	server {
		location PATH_PATTERN {
			proxy_read_timeout TIMEOUT;
		}
	}
}
```

## http.server.location.proxy_set_header

反向代理请求后端时的首部设置。可指定多个。

```
http {
	server {
		location PATH_PATTERN {
			proxy_set_header HEADER VALUE;
		}
	}
}
```

VALUE可使用`$http_upgrade`。

## http.server.rewrite

重定向的地址。

```
http {
	server {
		rewrite REQUEST_URI_PATTERN REDIRECT_URL [last|break|redirect|permanent];
	}
}
```

REQUEST_URI_PATTERN进行匹配时忽略请求的方案、主机和端口，从路径开始匹配。可使用正则表达式。

REDIRECT_URL可使用以下变量：

* $N
* $request_uri

## http.server.server_name

HTTP服务的主机名。请求的Host首部匹配该值。

```
http {
	server {
		server_name HOST;
	}
}
```

## http.server.ssl_certificate

HTTPS证书PEM文件路径。

```
http {
	server {
		ssl_certificate CERT.PEM;
	}
}
```

## http.server.ssl_certificate_key

HTTPS证书KEY文件路径。

```
http {
	server {
		ssl_certificate_key CERT.KEY;
	}
}
```

## http.server.ssl_ciphers

```
http {
	server {
		ssl_ciphers HIGH:!aNULL:!MD5;
	}
}
```

## http.server.ssl_prefer_server_ciphers

```
http {
	server {
		ssl_prefer_server_ciphers on;
	}
}
```

## http.server.ssl_session_cache

```
http {
	server {
		ssl_session_cache shared:SSL:1m;
	}
}
```

## http.server.ssl_session_timeout

```
http {
	server {
		ssl_session_timeout 5m;
	}
}
```

## http.server_names_hash_bucket_size

出现类似“nginx: [emerg] could not build server_names_hash, you should increase server_names_hash_bucket_size: 32”的错误时，加大该值。

```
http {
	server_names_hash_bucket_size N;
}
```

## user

运行工作进程的操作系统用户名。

```
user USER;
```

# 示例

## HTTP反向代理

```
http {
	# Other configurations...
	server {
		listen      80;
		server_name www.myweb.com;
		location / {
			proxy_pass http://localhost:81;
		}
	}
}
```

## HTTPS反向代理

```
http {
	# Other configurations...
	server {
		listen       443 ssl;
		server_name  www.myweb.com;

		ssl_certificate      /certificate/dir/cert.pem;
		ssl_certificate_key  /certificate/dir/key.key;

		ssl_session_cache    shared:SSL:1m;
		ssl_session_timeout  5m;

		ssl_ciphers  HIGH:!aNULL:!MD5;
		ssl_prefer_server_ciphers  on;

		location / {
			proxy_pass http://localhost:81;
		}
	}
}
```

## WebSocket反向代理

```
http {
	# Other configurations...
	server {
		listen      80;
		server_name www.myweb.com;
		location /websocket {
			proxy_pass http://localhost:81/websocket;
			proxy_read_timeout 1h;
			proxy_set_header Connection Upgrade;
			proxy_set_header Upgrade $http_upgrade;
		}
	}
}
```

## 重定向

```
http {
	server {
		listen      80;
		server_name www.myweb.com;
		rewrite .* https://www.herweb.com$request_uri;
	}
}
```
