本文更新于2023-02-27，使用Docker 23.0.1，操作系统为Debian 11。

Docker与宿主机共享同一个系统内核，容器是以root用户身份运行的，如果挂载了磁盘卷，则容器往磁盘卷创建文件的时候，宿主机的文件所有者就是root用户。

下文阐述如何通过gosu指定宿主机的用户来运行容器。示例的作用是，通过容器，使用宿主机当前用户在宿主机当前目录下创建文件。

1. 编写Docekrfile。
	```
	FROM debian:11
	
	RUN apt-get update
	RUN apt-get install --no-install-recommends -y gosu
	
	ARG UID
	
	RUN useradd -u ${UID} user
	
	ENTRYPOINT gosu user touch /data/dir/file
	```
	Dockerfile通过参数UID传入用来运行容器的宿主机用户ID。当然，也可以直接用具体值写死。user为容器中的用户名，这是无关紧要的，不和宿主机的用户名对应也可。
1. 构建Docker镜像。
	```shell
	docker image build . --build-arg UID=$(id -u) --tag test:v1
	```
	将宿主机当前用户的用户ID传入UID参数。当然，如果Dockerfile中写死了UID的值，就无需传入参数。或者，也可使用其它用户ID传入。
1. 创建Docker容器。
	```shell
	docker container create -v $(pwd)/dir:/data/dir --name testv1 test:v1
	```
1. 保证磁盘卷映射的宿主机目录存在，且用户有权限。
	```shell
	mkdir dir
	```
	如果磁盘卷映射的宿主机目录不存在，则容器运行时会先自行创建，其所有者就是root用户。
	
	因本文构建Docker镜像是使用当前用户，创建目录也是使用当前用户，故用户对此目录总是有权限的。
1. 运行容器。
	```shell
	docker start testv1
	```
1. 查看宿主机当前目录下新创建的文件的所有者。
	```shell
	ls -l dir/file
	```
	如文件所有者是宿主机当前用户，则运行正确。
