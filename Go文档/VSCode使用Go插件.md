本文更新于2024-09-06，使用VSCode 1.85.1、Go插件v0.42.0。

# 安装

1. 安装Go插件：

	点击侧边栏的“扩展”图标（Ctrl+Shift+X）打开扩展面板，搜索“Go”，安装“Go”插件（Go for Visual Studio Code）。
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

1. 使用上述方式启动VSCode并打开目录。
1. 打开对应的.go文件，使用“运行->启动调试”（F5）启动调试。
1. 使用“运行->停止调试”（Shift+F5）停止调试。

如需自定义调试配置，使用“运行->添加配置...”，选择“Go: Launch Package Debug/test the package of the open file”，则会在被打开的目录中创建配置文件.vscode/launch.json。文件内容如下：

```json
{
    // 使用 IntelliSense 了解相关属性。
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch Package",
            "type": "go",
            "request": "launch",
            "mode": "auto",
            "program": "${fileDirname}"
        }
    ]
}
```

`configurations`中的元素可使用的字段有：

* cwd：进程的运行目录。可为绝对路径或打开目录的相对路径。默认为`program`的值。
