本文更新于2024-12-15，使用Flutter 3.3.3，操作系统为Windows 10。

# 安装Flutter

1. 下载Flutter。官网下载页面为：[https://docs.flutter.dev/get-started/install/windows](https://docs.flutter.dev/get-started/install/windows)。

	本文使用的版本的下载地址为：[https://storage.googleapis.com/flutter_infra_release/releases/stable/windows/flutter_windows_3.3.3-stable.zip](https://storage.googleapis.com/flutter_infra_release/releases/stable/windows/flutter_windows_3.3.3-stable.zip)。
1. 解压zip文件。

	生成的默认目录名为flutter_windows_3.3.3-stable，下文记为$FLUTTER。
1. 设置环境变量`Path`。

	将$FLUTTER/flutter/bin的绝对路径加入环境变量`Path`。此目录下有可执行程序dart和flutter，可以运行以下命令查看环境变量是否正确设置：
	```bat
	dart --version
	flutter --version [--verbose]
	````
1. 进行自检。

	如配置不正确或缺少组件，则会有相应提示。还会为运行做首次配置。这一过程耗时较长。
	```bat
	flutter doctor
	```

## 环境变量

* FLUTTER_STORAGE_BASE_URL

## Flutter命令

* flutter analyze：运行代码分析器。
* flutter build apk|ios：编译Android/iOS的APK。
* flutter create [-a kotlin] [-i swift] PROJECT：创建项目。
* flutter doctor：自检。
* flutter doctor --android-license：同意Android SDK的许可协议。
* flutter drive --target=FILENAME：运行集成测试。如为相对路径，则以项目目录为根。
* flutter logs：查看日志。
* flutter packages get：获取项目的依赖库。
* flutter packages upgrade：升级项目的依赖库至最新版本。
* flutter run [-d chrome --web-renderer html|canvaskit] [--trace-startup --profile]：运行项目。输入r可进行热修复。--trace-startup --profile用于启动时间分析，但不能使用虚拟设备。
* flutter test [FILENAME]：运行单元测试和组件测试。如为相对路径，则以项目目录为根；若不指定文件，则运行所有测试。
* flutter upgrade：升级。升级完后自动执行自检。

# 使用Android Studio开发

本文使用Android Studio 2021.3.1。

## 安装Android Studio

1. 下载Android Studio。官网下载页面为：[https://developer.android.google.cn/studio/](https://developer.android.google.cn/studio/)。

	本文使用的版本的下载地址为：[https://redirector.gvt1.com/edgedl/android/studio/install/2021.3.1.16/android-studio-2021.3.1.16-windows.exe](https://redirector.gvt1.com/edgedl/android/studio/install/2021.3.1.16/android-studio-2021.3.1.16-windows.exe)
1. 安装Android Studio。
1. 启动Android Studio。第一次启动时会进行一些配置，并下载一些文件。

	“Verify Settings”界面会列出所有的配置项，“SDK Components to Download”项如为空，则可能需修改host文件，增加一行：
	```
	203.208.40.97 dl.google.com
	```

## Android Studio安装插件

1. 安装Flutter插件，会自动安装Dart插件。

	安装完成需要重启Android Studio，会出现“New Flutter Project”选项。

## Android Studio运行项目

1. 创建虚拟设备。

	在虚拟设备管理器“Virtual Device Manager”中创建虚拟设备。
1. 创建Flutter项目。

	使用“New Flutter Project”创建项目，选择“Flutter”并填写“Flutter SDK path”（即$FLUTTER/flutter的绝对路径），创建的时候可能耗时较长。“Project Name”必需以小写开头，且不可有空格。如“Platforms”勾选了“Web”，则可以使用浏览器作为虚拟设备。
1. 运行项目。

	选择一个虚拟设备，入口点默认为main.dart文件，点击绿色三角形按钮，编译并在虚拟设备上运行。这一过程耗时较长。默认的程序是一个点击计数器。

# 使用VSCode开发

本文使用VSCode 1.71.2。

## 安装VSCode

## VSCode安装插件

1. 在VSCode扩展中安装Flutter插件，会自动安装Dart插件。

## VSCode运行项目

1. 创建Flutter项目。

	使用快捷键Ctrl+Shift+P，选择“Flutter: New Project”创建项目。

	“Application”为创建应用程序项目。
1. 选择设备。
	
	使用快捷键Ctrl+Shift+P，选择“Flutter: Select Device”，再选择对应的设备。
1. 运行项目。

	点击VSCode右上方的三角形图标，选择“Start Debugging”开始调试，或选择“Run Without Debugging”直接运行不调试。这一过程耗时较长，默认的应用程序是一个点击计数器。

	可使用快捷键Ctrl+Shift+D打开“运行和调试”面板，选择对应的配置。
1. 热重载。

	点击黄色闪电图标或快捷键Ctrl+F5，可进行热重载。

## 配置.vscode/launch.json

如需对VSCode项目进行配置，则在项目目录下创建.vscode/launch.json（或在“运行和调试”面板快速创建）。文件内容示例如下：

```json
{
	// 使用 IntelliSense 了解相关属性。 
	// 悬停以查看现有属性的描述。
	// 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
	"version": "0.2.0",
	"configurations": [
		{
			"name": "testdemo",
			"request": "launch",
			"type": "dart",
			// "args": ["--web-renderer html"]
		}
	]
}
```
