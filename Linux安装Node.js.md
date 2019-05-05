# 说明

本文写于2017-10-03，使用Node.js v0.12.0，操作系统为64位CentOS Linux release 7.2.1511 (Core)。

# 安装

以下部分步骤需要root权限。

```shell
wget https://nodejs.org/dist/v0.12.0/node-v0.12.0-linux-x64.tar.gz
tar -xz -f node-v0.12.0-linux-x64.tar.gz
cd node-v0.12.0-linux-x64
mkdir -p /usr/local/share/man/man1
cp share/man/man1/node.1 /usr/local/share/man/man1
cp -r include/node /usr/local/include
cp -r lib/node_modules /usr/local/lib
cp bin/node /usr/local/bin/
cd /usr/local/bin
ln -s /usr/local/lib/node_modules/npm/bin/npm-cli.js npm
```

步骤说明：

1. 如需安装其他版本的Node.js，请到官网下载。
1. 略。
1. 略。
1. 略。
1. man文件。
1. C++头文件。
1. 全局node_modules。
1. ndoe命令。
1. 略。
1. npm命令。
