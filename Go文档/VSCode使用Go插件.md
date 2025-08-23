本文更新于2025-08-07，使用VSCode 1.98.2、Go插件v0.48.0。

# 安装

1. 安装Go插件：

	点击图标打开侧边栏的“扩展”（Ctrl+Shift+X）面板，搜索“Go”，安装“Go”插件（Go for Visual Studio Code）。
1. 安装Go插件的依赖工具：

	1. 因国内无法访问proxy.golang.org，故需自行配置模块代理。可使用两种方法：
		* 在Windows系统中设置GOPROXY环境变量为https://goproxy.cn（详细步骤略），然后启动VSCode。
		* 在cmd中运行`set GOPROXY=https://goproxy.cn && code`来启动VSCode。
	1. 使用“帮助->显示所有命令”（Ctrl+Shift+P）打开命令面板，输入“Go: Install/UPdate Tools”，选择所有依赖工具后确定。目前的依赖工具有：
		* gopls：v0.16.1
		* gotests：v1.6.0
		* gomodifytags：v1.16.0
		* impl：v1.1.0
		* goplay：v1.0.0
		* dlv：v1.23.0
		* staticcheck：v0.4.7
	1. 安装完成后会提示“All tools successfully installed. You are ready to Go. :)”。依赖工具安装于$GOPAHT/bin目录中（$GOPATH可使用`go env GOPATH`查看）。

# 使用

使用“文件->打开文件夹...”（Ctrl+K Ctrl+O）打开目录。即可对目录树中的Go代码使用代码补全、代码跳转等功能。

VSCode在加载代码时，因国内无法访问proxy.golang.org，当未自行配置模块代理时，会导致加载代码所需的时间较长，此时的错误可于输出面板选择“gopls(server)”进行查看。故需自行配置模块代理，可使用两种方法：

* 在Windows系统中设置GOPROXY环境变量为https://goproxy.cn（详细步骤略），然后启动VSCode。
* 运行bat脚本启动VSCode，脚本内容如下：
	```bat
	set GOPROXY=https://goproxy.cn
	code
	```

# 调试

1. 使用上述方式启动VSCode。
1. 使用“文件->打开文件夹...”（Ctrl+K Ctrl+O）打开工程目录。工程目录可为go.mod所在的目录，也可为其父目录。
1. 使用“运行->添加配置...”，选择“Go: Launch Package	Debug/test the package of the open file”，会在被打开的目录中创建配置文件.vscode/launch.json。根据需要修改配置。此步骤如省略，则不使用配置进行调试。
1. 点击图标打开侧边栏的“运行和调试”（Ctrl+Shift+D）面板，选择对应的配置。如未添加配置，则必需打开对应的.go文件。
1. 使用“运行->启动调试”（F5）启动调试。
1. 使用“运行->停止调试”（Shift+F5）停止调试。

配置文件字段有：

* configurations：配置列表。
	* cwd：进程的运行目录。可为绝对路径或打开目录的相对路径。默认为`program`的值。
	* mode
	* name：配置名。用于在“运行和调试”面板中下拉选择。
	* program：需编译运行的程序路径。可为.go文件或main包目录。
	* request
	* type
* version

配置值可使用`${VARIABLE}`形式指定变量，可用的变量有：

* ${fileDirname}：当前打开的文件的完整目录名。
* ${workspaceFolder}：在 VS Code 中打开的文件夹的路径。

# 命令

使用“帮助->显示所有命令”（Ctrl+Shift+P）打开命令面板后，可使用的命令如下：

* Go: Add Import：添加import行。
* Go: Install/UPdate Tools：安装/更新Go插件的依赖工具。
