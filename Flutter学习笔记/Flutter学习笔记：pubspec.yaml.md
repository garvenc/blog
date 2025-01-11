本文更新于2024-12-15，使用Flutter 3.3.3。

[TOC]

# 项目结构

* android/：Android原生目录。
	* app/
		* build.gradle
		* src/
			* main/
				* AndroidMainfest.xml：Android重要配置。
				* res/
					* drawable/
						* launch_background.xml：启动页配置。
					* midmap-hdpi/
						* ic_lancher.png：启动图标。
					* midmap-mdpi/
						* ic_lancher.png：启动图标。
					* midmap-xhdpi/
						* ic_lancher.png：启动图标。
					* midmap-xxhdpi/
						* ic_lancher.png：启动图标。
					* midmap-xxxhdpi/
						* ic_lancher.png：启动图标。
* build/：构建目录。
	* app/
		* outputs/
			* apk/
				* release/
					* app-release.apk：Android发布版本APK。
	* start_up_info.json：启动时间分析结果。各字段含义如下：engineEnterTimestampMicros为进入Flutter框架引擎所需的微秒数，timeToFirstFrameMicros为显示第一帧所需的微秒数，timeToFrameworkInitMicros为初始化Flutter框架所需的微秒数，timeAfterAfterFrameworkInitMicros为完成初始化Flutter框架所需的微秒数。
* ios/：iOS原生目录。
	* Runner/
		* Assets.xcassets/：启动图标目录。
			* Contents.json
			* Icon-App-20x20@1x.png
			* Icon-App-20x20@2x.png
			* Icon-App-20x20@3x.png
			* Icon-App-29x29@1x.png
			* Icon-App-29x29@2x.png
			* Icon-App-29x29@3x.png
			* Icon-App-40x40@1x.png
			* Icon-App-40x40@2x.png
			* Icon-App-40x40@3x.png
			* Icon-App-60x60@2x.png
			* Icon-App-60x60@3x.png
			* Icon-App-76x76@1x.png
			* Icon-App-76x76@2x.png
			* Icon-App-83.5x83.5@2x.png
			* Icon-App-1024x1024@1x.png
			* LaunchImage.imageset/：启动页目录。
				* Contents.json：启动页内容配置文件。
				* LaunchImage.png
				* LaunchImage@2x.png
				* LaunchImage@3x.png
* lib/：Flutter的Dart源代码目录。
	* main.dart：主函数入口。
	* src/：私有源代码目录。
* pubspec.yaml：配置文件。
* test/：测试的Dart源代码目录。
* test_driver/：集成测试的Dart源代码目录。默认不包含此目录。集成测试类文件名必需为指令化应用程序类文件名后加“_test”。

# pubspec.yaml

* dependencies：依赖库列表。可包含外部依赖库。
	* cupertino_icons：Cupertino（iOS）风格库。
	* flutter：Flutter库。
		* sdk：为flutter。
	* flutter_localizations：多语言本地化库。
		* sdk：为flutter。
* dev_dependencies：开发依赖库。
	* flutter_driver：Flutter集成测试用到。
		* sdk：为flutter。
	* flutter_test：Flutter测试库。
		* sdk：为flutter。
* flutter
	* assets：资源文件夹的文件列表。为相对于项目根目录的路径。基本同名的文件会被认为是变体（Variant）。
	* fonts：字体族列表。
		* family：字体族名称。
		* fonts：字体列表。
			* asset：字体文件路径。为相对于项目根目录的路径。如字体在某个包中，则格式为packages/PACKAGENAME/FILEPATH。
	* uses-material-design

# 外部依赖库

以下是常见的外部依赖库，更多外部依赖库可访问[https://pub.dev](https://pub.dev)，或中文站[https://pub-web.flutter-io.cn])(https://pub-web.flutter-io.cn)。

* camera：视频录制。
* event_bus：事件总线。用于全局事件广播。
* flutter_blue：蓝牙。
* flutter_nfc_reader：NFC。
* flutter_sound：音频录放。
* geolocator：定位信息。
* image_picker：相机和相册。
* path_provider：文件读写。
* proximity_plugin：距离传感器。
* shared_preferences：首选项。
* sqflite：SQLite。
* url_launcher：从URL加载网页。
* video_player：视频播放。支持播放本地和网络视频。
