本文更新于2023-12-26，使用VSCode 1.85.1、Go插件v0.40.1。

1. 安装Go插件：

	使用Ctrl+Shift+X打开扩展面板，搜索“Go”，安装“Go”插件（Go for Visual Studio Code）。
1. 安装Go插件的依赖工具：

	因国内无法访问proxy.golang.org，故需自行配置模块代理。
	1. 运行cmd。
	1. 在cmd中运行`set GOPROXY=https://goproxy.cn`。
	1. 在cmd中运行`code`启动VSCode。
	1. 使用Ctrl+Shift+P打开命令面板，输入“Go: Install/UPdate Tools”，选择所有依赖工具后确定。目前的依赖工具有：
		* gopls
		* gotests
		* gomodifytags
		* impl
		* goplay
		* dlv
		* staticcheck
	1. 安装完成后会提示“All tools successfully installed. You are ready to Go. :)”。依赖工具安装于$GOPAHT/bin目录中（$GOPATH可使用`go env GOPATH`查看）。
1. 使用VSCode打开你项目的模块目录（目录中有go.mod文件），即可使用代码补全和代码跳转等功能。

	VSCode在加载项目代码时，会执行go mod tidy，若项目的依赖模块未下载至模块缓存中，会自动进行下载。当未自行配置模块代理时，因国内无法访问proxy.golang.org，会导致加载项目代码所需的时间较长。此时的错误提示可于输出面板选择“gopls(server)”进行查看。要避免此问题，可使用bat脚本启动VSCode，脚本内容如下：
	```bat
	set GOPROXY=https://goproxy.cn
	code
	```
