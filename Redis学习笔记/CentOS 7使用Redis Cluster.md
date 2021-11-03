本文更新于2021-11-03，使用Redis 4.0.8，操作系统为CentOS 7.5。

1. CentOS 7下直接运行`redis-trib.rb`可能出现如下提示，原因是没有安装Ruby：

	> /usr/bin/env: ruby: No such file or directory
1. 安装Ruby。

	```shell
	yum install ruby
	```
1. 安装Ruby包管理器。

	```shell
	yum install rubygems
	```
1. 通过Ruby包管理器安装Redis模块。

	```shell
	gem install redis
	```
	
	可能出现如下提示，原因是Ruby版本过低：
	
	> ERROR:  Error installing redis:
	>         redis requires Ruby version >= 2.4.0.
1. 使用RMV（Ruby版本管理器）安装高版本Ruby。

	1. 安装RVM，官方文档见：[https://rvm.io/](https://rvm.io/)。
	
		```shell
		\curl -sSL https://get.rvm.io | bash -s stable
		```
		
		可能出现如下提示，原因是未安装公钥：
		
		> Warning, RVM 1.29.12 introduces signed releases and automated check of signatures when GPG software found.
		> Assuming you trust Michal Papis import the mpapis public key (downloading the signatures).
		> GPG signature verification failed for '/home/tigergm/.rvm/archives/rvm-1.29.7.tgz' - 'https://github.com/rvm/rvm/releases/download/1.29.7/1.29.7.tar.gz.asc'! Try to install GPG v2 and then fetch the public key:
		>     gpg2 --keyserver hkp://pool.sks-keyservers.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
		> or if it fails:
		>     command curl -sSL https://rvm.io/mpapis.asc | gpg2 --import -
		>     command curl -sSL https://rvm.io/pkuczynski.asc | gpg2 --import -
		> In case of further problems with validation please refer to https://rvm.io/rvm/security
		
		根据提示，先运行：
		```shell
		gpg2 --keyserver hkp://pool.sks-keyservers.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
		```
		
		如失败改为运行：
		
		```shell
		curl -sSL https://rvm.io/mpapis.asc | gpg2 --import -
		curl -sSL https://rvm.io/pkuczynski.asc | gpg2 --import -
		```
		
		然后再次运行：
		
		```shell
		\curl -sSL https://get.rvm.io | bash -s stable
		```
	1. 使RVM立即生效。
	
		```shell
		source /usr/local/rvm/scripts/rvm
		```
	1. 查看RVM库中的Ruby版本。
	
		```shell
		rvm list known
		```
	1. 安装指定版本的Ruby（此处安装2.4.1，可根据需要选择）。
	
		```shell
		rvm install 2.4.1
		```
	1. 切换Ruby默认版本。
	
		```shell
		rvm use 2.4.1 --default
		```
	1. 查看Ruby版本。
	
		```shell
		ruby --version
		```
1. 重新通过Ruby包管理器安装Redis模块。

	```shell
	gem install redis
	```
1. 重新运行`redis-trib.rb`。
