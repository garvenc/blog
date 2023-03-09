本文更新于2023-02-28，使用Docker 23.0.1。

[TOC]

官方文档参阅：[https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)

Dockerfile所在的目录为构建上下文，Docker会在构建镜像时将构建上下文和该上下文中的文件和目录上传到Docker引擎。如目录中存在文件.dockerignore，则匹配其中规则的文件将不会上传。

Dockerfile由一系列指令和参数构成，以“#”开头的行视为注释。Dockerfile会从上至下执行，每条指令都会创建一个新的镜像层并对镜像进行提交。

示例：

```
# Version: 0.0.1

FROM ubuntu:14.04

MAINTAINER GV "gv@example.com"

ENV REFRESHED_AT 2014-07-01

ADD ./index.html /usr/share/nginx/html/index.html

RUN apt-get update && \
	apt-get install -y nginx

WORKDIR /usr/local/nginx/sbin

ENTRYPOINT ./nginx

EXPOSE 80
```

**说明：本文中，小写为自定义变量，根据实际情况填写。使用`<>`引起表示内容可选，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

# ADD

构建时将构建上下文中的文件或目录添加到镜像中。会令从`ADD`指令起都无法使用构建缓存。

```
ADD src dst
```

src也可以使用URL。如果src是gzip、bzip2、xz归档文件，则会自动将其解包至dst目录下，如已存在同名文件，则不会覆盖。

如dst以/结尾，则认为是复制至目录；否则，则认为是复制至文件。如dst不存在，则会自动创建，创建的文件和目录的权限都是0755，且GID和UID都是0。

# ARG

指定构建时使用的变量。

```
ARG name<=value>
```

在Dockerfile中使用$name引用变量。

Docker预定义了以下变量：

* HTTP_PROXY
* http_proxy
* HTTPS_PROXY
* https_proxy
* FTP_PROXY
* ftp_proxy
* NO_PROXY
* no_proxy

# CMD

指定容器启动时要运行的命令，只有最后一条CMD指令有效，且其会被`docker run`指定的命令覆盖。

```
CMD ["command", "arg" <, ...>]
CMD command arg <...>
```

推荐使用第一种，以数组形式表示参数。第二种会在执行时使用`/bin/sh -c`包装执行，可能导致意料之外的行为，且不能在不支持shell的平台上执行。

# COPY

构建时将构建上下文中的文件或目录复制到镜像中。类似`ADD`，但不会做文件提取和解压。

```
COPY src dst
```

src为目录，则复制目录下的内容。

dst必需为绝对路径。如dst以/结尾，则认为是复制至目录下；否则，则认为是复制至文件。如dst不存在，则会自动创建，创建的文件和目录的权限都是0755，且GID和UID都是0。

# ENTRYPOINT

指定容器启动时要运行的命令的入口点，`docker run`和`CMD`指令指定的任何参数都会被当作参数再次传递给`ENTRYPOINT`指令中指定的命令。

```
ENTRYPOINT ["command", "arg" <, ...>]
ENTRYPOINT command arg <...>
```

类似--param value可写成`"--param", "value"`或`"--param value"`。

推荐使用第一种，以数组形式表示参数。第二种会在执行时使用`/bin/sh -c`包装执行，可能导致意料之外的行为，且不能在不支持shell的平台上执行。

# ENV

在镜像中设置环境变量。该环境变量可在后续的指令和启动的容器中使用。

```
ENV name value
ENV name=value <...>
```

环境变量名environment_variable通常使用大写。

# EXPOSE

说明容器内的应用程序需公开那个端口。Docker并不会自动开放端口，需要在运行容器时指定。

```
EXPOSE port</protocol> <...>
```

# FROM image

指定构建的基础镜像。第一条指令必需为`FROM`。

```
FROM image
```

# LABEL

为镜像添加元数据标记。

```
LABEL name="value" <...>
```

推荐将所有元数据标记都放到一条指令中，以防止创建过多镜像层。

# MAINTAINER

指定作者和电子邮箱。

```
MAINTAINER author "email"
```

# ONBUILD

添加构建触发器。当镜像被用作其它镜像的（直接）基础镜像时，指定的指令将会被执行。

```
ONBUILD directive
```

directive指定的指令不可以使用`FROM`、`MAINTAINER`、`ONBUILD`。

触发器会在构建过程中插入新指令，可以认为插入的指令是紧跟在`FROM`之后。

# RUN

指定构建时运行的命令。

```
RUN ["command", "arg" <, ...>]
RUN command arg <...>
```

推荐使用第一种，以数组形式表示参数。第二种会在执行时使用`/bin/sh -c`包装执行，可能导致意料之外的行为，且不能在不支持shell的平台上执行。

# STOPSIGNAL

指定发送给容器用以停止容器的系统信号。

```
STOPSIGNAL signal
```

# USER

指定以容器内部哪个用户运行，默认为root。

```
USER user|uid<:group|gid>
```

# VOLUME

向容器添加卷。卷可用于共享数据或对数据进行持久化。

```
VOLUME ["mountdir" <, ...>]
VOLUME mountdir
```

如在`docker run`或`docker create`未通过-v将宿主机的目录作为卷挂载到容器中，则会在宿主机/var/lib/docker/volumes下先创建目录再进行挂载。

# WORKDIR

指定构建镜像时容器内部的工作目录。如目录不存在，则会自动创建。`ENTRYPOINT`、`CMD`指令和根目录`/`都会在此目录下执行。

```
WORKDIR dir
```
