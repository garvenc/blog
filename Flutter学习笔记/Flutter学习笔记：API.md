本文更新于2024-12-26，使用Flutter 3.3.3。

官方文档：[https://api.flutter.dev](https://api.flutter.dev)或[https://api.flutter-io.cn](https://api.flutter-io.cn)

[TOC]

# dart:ui 【UI库】

```dart
import 'dart:ui';
```

## 枚举

### TextDirection —— 文本方向

* 继承
	Object > Enum > TextDirection
* 值
	* rtl → const TextDirection：从右到左。
	* ltr → const TextDirection：从左到右。

# animation 【动画库】

```dart
import 'package:flutter/animation.dart';
```

Flutter支持120FPS（Frames Per Second，帧每秒）的帧率。

## 类

### AnimatedBuilder —— 动画创建器

可用于实现动画叠加，即交错动画。

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > AnimatedWidget > ListenableBuilder > AnimatedBuilder
* 构造函数
	```dart
	AnimatedBuilder({
		Key? key,
		required Listenable animation,
		required TransitionBuilder builder,
		Widget? child
	})
	```

### Animation\<T> —— 动画

一方面生成动画的值，另一方面用来得知动画的状态。但是无法通过它得知动画的具象，且和渲染无关。

* 继承
	Object > Listenable > Animation
* 构造函数
	```dart
	Animation()
	```
* 属性
	* value → T：动画的值。
* 方法
	* addListener(VoidCallback listener) → void：添加事件监听器。在动画的值发生变化时触发。
	* addStatusListener(AnimationStatusListener listener) → void：添加状态监听器。在动画的状态发生变化时触发。

### AnimationController —— 动画控制器

管理`Animation`。

* 继承
	Object > Listenable > Animation\<double> > AnimationController
* 构造函数
	```dart
	AnimationController({
		double? value,
		Duration? duration,
		Duration? reverseDuration,
		String? debugLabel,
		double lowerBound = 0.0,
		double upperBound = 1.0,
		AnimationBehavior animationBehavior = AnimationBehavior.normal,
		required TickerProvider vsync
	})
	```

	* vsync：垂直同步的区域。限制此区域在执行动画的区域内，可避免消耗过多资源。
* 属性
	* duration ↔ Duration?：动画的持续时间。
* 方法
	* dispose() → void：销毁动画。
	* forward({double? from}) → TickerFuture：正向播放动画。
	* reset() → void：重置动画至初始位置。
	* reverse({double? from}) → TickerFuture：反向播放动画。
	* stop({bool canceled = true}) → void：停止播放动画。

### ColorTween —— 颜色补间

* 继承
	Object > Animatable\<Color?> > Tween\<Color?> > ColorTween
* 构造函数
	```dart
	ColorTween({Color? begin, Color? end})
	```

### Curve —— 曲线

* 继承
	Object > ParametricCurve\<double> > Curve
* 构造函数
	```dart
	Curve()
	```

### CurvedAnimation —— 曲线动画/非线性动画

将动画的过程抽象成非线性曲线。

* 继承
	Object > Listenable > Animation\<double> > CurvedAnimation
* 构造函数
	```dart
	CurvedAnimation({
		required Animation<double> parent,
		required Curve curve,
		Curve? reverseCurve
	})
	```

### Curves —— 曲线集

* 常量
	* bounceIn → const Curve：气球跳跃式。
	* ease → const Cubic
	* linear → const Curve：线性。

### Interval —— 时间间隔

可以用来定义动画在执行上的时间线顺序。

* 继承
	Object > ParametricCurve\<double> > Curve > Interval
* 构造函数
	```dart
	Interval(
		double begin,
		double end,
		{
		Curve curve = Curves.linear
		}
	)
	```
* 属性
	* begin → double：起始时间点在动画时间轴的百分比。
	* end → double：结束时间点在动画时间轴的百分比。

### Locale —— 语系区域

* 构造函数
	```dart
	Locale(String _languageCode, [String? _countryCode])
	```

### Tween\<T extends Object?> —— 补间

定义正在执行动画的对象所使用的数据范围内的值。

* 继承
	Object > Animatable\<T> > Tween
* 构造函数
	```dart
	Tween({
		T? begin,
		T? end
	})
	```
* 方法
	* animate(Animation\<double> parent) → Animation\<T>：生成动画。

## 枚举

### AnimationStatus —— 动画状态

* 值
	* dismissed → const AnimationStatus
	* forward → const AnimationStatus
	* reverse → const AnimationStatus
	* completed → const AnimationStatus

# flutter_localizations 【多语言本地化库】

## 类

### GlobalMaterialLocalizations —— Material风格组件的多语言本地化

* 常量
	* delegate → const LocalizationsDelegate\<MaterialLocalizations>

### GlobalWidgetsLocalizations —— 组件的多语言本地化

* 常量
	* delegate → const LocalizationsDelegate\<WidgetsLocalizations>

# cupertino 【Cupertino（iOS）风格库】

```dart
import 'package:flutter/cupertino.dart';
```

与Material的类比关系：

| Cupertino               | Material                 |
| ----------------------- | ------------------------ |
| CupertinoApp            | MaterialApp              |
| CupertinoPageScaffold   | Scaffold                 |
| CupertinoTabScaffold    | Scaffold                 |
| CupertinoNavigationBar  | AppBar                   |
| CupertinoTabBar         | BottomNavigationBar      |
| CupertinoAlertDialog    | AlertDialog              |
| CupertinoSlider         | Slider                   |

## 类

### CupertinoActionSheet —— Cupertino设计风格的操作表

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > CupertinoActionSheet
* 构造函数
	```dart
	CupertinoActionSheet({
		Key? key,
		Widget? title,
		Widget? message,
		List<Widget>? actions,
		ScrollController? messageScrollController,
		ScrollController? actionScrollController,
		Widget? cancelButton
	})
	```
* 属性
	* actions → List\<Widget>?：操作项列表。
	* cancelButton → Widget?：取消按钮。
	* message → Widget?：描述。
	* title → Widget?：标题。

### CupertinoActionSheetAction —— Cupertino设计风格的操作表的操作项

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > CupertinoActionSheetAction
* 构造函数
	```dart
	CupertinoActionSheetAction({
		Key? key,
		required VoidCallback onPressed,
		bool isDefaultAction = false,
		bool isDestructiveAction = false,
		required Widget child
	})
	```

### CupertinoActivityIndicator —— Cupertino设计风格的动作指示器

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > CupertinoActivityIndicator
* 构造函数
	```dart
	CupertinoActivityIndicator({
		Key? key,
		Color? color,
		bool animating = true,
		double radius = _kDefaultIndicatorRadius
	})
	```
* 属性
	* animating → bool：是否具有动画效果。默认为true。
	* radius → double：半径。默认为10。

### CupertinoAlertDialog —— Cupertino设计风格的提示框

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > CupertinoAlertDialog
* 构造函数
	```dart
	CupertinoAlertDialog({
		Key? key,
		Widget? title,
		Widget? content,
		List<Widget> actions = const <Widget>[],
		ScrollController? scrollController,
		ScrollController? actionScrollController,
		Duration insetAnimationDuration = const Duration(milliseconds: 100),
		Curve insetAnimationCurve = Curves.decelerate
	})
	```
* 属性
	* actions → List\<Widget>：操作列表。不多于两个时水平排列，多余两个时垂直排列。
	* content → Widget?：内容。
	* title → Widget?：标题。

### CupertinoApp —— Cupertino设计风格APP

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > CupertinoApp
* 构造函数
	```dart
	CupertinoApp({
		Key? key,
		GlobalKey<NavigatorState>? navigatorKey,
		Widget? home,
		CupertinoThemeData? theme,
		Map<String,
		Widget Function(BuildContext)> routes = const <String, WidgetBuilder>{},
		String? initialRoute,
		RouteFactory? onGenerateRoute,
		InitialRouteListFactory? onGenerateInitialRoutes,
		RouteFactory? onUnknownRoute,
		NotificationListenerCallback<NavigationNotification>? onNavigationNotification,
		List<NavigatorObserver> navigatorObservers = const <NavigatorObserver>[],
		TransitionBuilder? builder,
		String title = '',
		GenerateAppTitle? onGenerateTitle,
		Color? color,
		Locale? locale,
		Iterable<LocalizationsDelegate>? localizationsDelegates,
		LocaleListResolutionCallback? localeListResolutionCallback,
		LocaleResolutionCallback? localeResolutionCallback,
		Iterable<Locale> supportedLocales = const <Locale>[Locale('en', 'US')],
		bool showPerformanceOverlay = false,
		bool checkerboardRasterCacheImages = false,
		bool checkerboardOffscreenLayers = false,
		bool showSemanticsDebugger = false,
		bool debugShowCheckedModeBanner = true,
		Map<ShortcutActivator, Intent>? shortcuts,
		Map<Type, Action<Intent>>? actions,
		String? restorationScopeId,
		ScrollBehavior? scrollBehavior,
		@Deprecated(
			'Remove this parameter as it is now ignored. '
			'CupertinoApp never introduces its own MediaQuery; the View widget takes care of that. '
			'This feature was deprecated after v3.7.0-29.0.pre.'
		)
		bool useInheritedMediaQuery = false})
	```

### CupertinoButton —— Cupertino设计风格的按钮

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > CupertinoButton
* 构造函数
	```dart
	CupertinoButton({
		Key? key,
		required Widget child,
		EdgeInsetsGeometry? padding,
		Color? color,
		Color disabledColor = CupertinoColors.quaternarySystemFill,
		double? minSize = kMinInteractiveDimensionCupertino,
		double? pressedOpacity = 0.4,
		BorderRadius? borderRadius = const BorderRadius.all(Radius.circular(8.0)),
		AlignmentGeometry alignment = Alignment.center,
		Color? focusColor,
		FocusNode? focusNode,
		ValueChanged<bool>? onFocusChange,
		bool autofocus = false,
		required VoidCallback? onPressed
	})
	```
* 属性
	* borderRadius → BorderRadius?：圆角的半径。仅在设置了`color`时生效。
	* child → Widget：子组件。
	* color → Color?：颜色。默认无颜色。
	* disabledColor → Color：在不可用时的颜色。
	* enabled → bool：是否可用。默认为false。可通过给`onPressed`赋值来使其可用。
	* onPressed → VoidCallback?：点击时的回调。

### CupertinoColors —— Cupertino设计风格的颜色集

### CupertinoDatePicker —— Cupertino设计风格的日期时间选择器

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > CupertinoDatePicker
* 构造函数
	```dart
	CupertinoDatePicker({
		Key? key,
		CupertinoDatePickerMode mode = CupertinoDatePickerMode.dateAndTime,
		required ValueChanged<DateTime> onDateTimeChanged,
		DateTime? initialDateTime,
		DateTime? minimumDate,
		DateTime? maximumDate,
		int minimumYear = 1,
		int? maximumYear,
		int minuteInterval = 1,
		bool use24hFormat = false,
		DatePickerDateOrder? dateOrder,
		Color? backgroundColor,
		bool showDayOfWeek = false,
		double itemExtent = _kItemExtent,
		SelectionOverlayBuilder? selectionOverlayBuilder
	})
	```
* 属性
	* initialDateTime → DateTime：初始日期时间。
	* onDateTimeChanged → ValueChanged\<DateTime>：日期时间改变时的回调。
	* use24hFormat → bool：是否使用24小时制。

### CupertinoIcons —— Cupertino设计风格的图标集

### CupertinoNavigationBar —— Cupertino设计风格的导航栏

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > CupertinoNavigationBar
* 构造函数
	```dart
	CupertinoNavigationBar({
		Key? key,
		Widget? leading,
		bool automaticallyImplyLeading = true,
		bool automaticallyImplyMiddle = true,
		String? previousPageTitle,
		Widget? middle,
		Widget? trailing,
		Border? border = _kDefaultNavBarBorder,
		Color? backgroundColor,
		Brightness? brightness,
		EdgeInsetsDirectional? padding,
		bool transitionBetweenRoutes = true,
		Object heroTag = _defaultHeroTag
	})
	```
* 属性
	* automaticallyImplyLeading → bool：是否自动生成左侧的返回按钮。默认为true。
	* backgroundColor → Color?：背景色。
	* leading → Widget?：导航栏开头的组件。
	* middle → Widget?：导航栏中间的组件。
	* previousPageTitle → String?：上一个页面的标题。在左侧返回按钮旁显示。
	* trailing → Widget?：导航栏结尾的组件。

### CupertinoPageRoute\<T> —— Cupertino设计风格的页面路由

* 继承
	Object > Route\<T> > OverlayRoute\<T> > TransitionRoute\<T> > ModalRoute\<T> > PageRoute\<T> > CupertinoPageRoute
* 构造函数
	```dart
	CupertinoPageRoute({
		required WidgetBuilder builder,
		String? title,
		RouteSettings? settings,
		bool maintainState = true,
		bool fullscreenDialog = false,
		bool allowSnapshotting = true,
		bool barrierDismissible = false
	})
	```

### CupertinoPageScaffold —— Cupertino设计风格的单页面脚手架

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > CupertinoPageScaffold
* 构造函数
	```dart
	CupertinoPageScaffold({
		Key? key,
		ObstructingPreferredSizeWidget? navigationBar,
		Color? backgroundColor,
		bool resizeToAvoidBottomInset = true,
		required Widget child
	})
	```
* 属性
	* backgroundColor → Color?：脚手架内所有组件的颜色。
	* child → Widget：界面主要内容。
	* navigationBar → ObstructingPreferredSizeWidget?：顶部导航栏。

### CupertinoPicker —— Cupertino设计风格的选择器

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > CupertinoPicker
* 构造函数
	```dart
	CupertinoPicker({
		Key? key,
		double diameterRatio = _kDefaultDiameterRatio,
		Color? backgroundColor,
		double offAxisFraction = 0.0,
		bool useMagnifier = false,
		double magnification = 1.0,
		FixedExtentScrollController? scrollController,
		double squeeze = _kSqueeze,
		required double itemExtent,
		required ValueChanged<int>? onSelectedItemChanged,
		required List<Widget> children,
		Widget? selectionOverlay = const CupertinoPickerDefaultSelectionOverlay(),
		bool looping = false
	})
	```
	
	* children：子组件列表。
	* looping：是否循环显示。
* 属性
	* backgroundColor → Color?：背景色。
	* itemExtent → double：每个项的高度。
	* onSelectedItemChanged → ValueChanged\<int>?：选中项变化时的回调。

### CupertinoSlider —— Cupertino设计风格的滑块

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > CupertinoSlider
* 构造函数
	```dart
	CupertinoSlider({
		Key? key,
		required double value,
		required ValueChanged<double>? onChanged,
		ValueChanged<double>? onChangeStart,
		ValueChanged<double>? onChangeEnd,
		double min = 0.0,
		double max = 1.0,
		int? divisions,
		Color? activeColor,
		Color thumbColor = CupertinoColors.white
	})
	```

### CupertinoTabBar —— Cupertino设计风格的选项卡栏

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > CupertinoTabBar
* 构造函数
	```dart
	CupertinoTabBar({
		Key? key,
		required List<BottomNavigationBarItem> items,
		ValueChanged<int>? onTap,
		int currentIndex = 0,
		Color? backgroundColor,
		Color? activeColor,
		Color inactiveColor = _kDefaultTabBarInactiveColor,
		double iconSize = 30.0,
		double height = _kTabBarHeight,
		Border? border = const Border(top: BorderSide(color: _kDefaultTabBarBorderColor, width: 0.0))
	})
	```
* 属性
	* activeColor → Color?：选中选项卡的颜色。
	* currentIndex → int：选中选项卡的下标。
	* inactiveColor → Color：未选中选项卡的颜色。
	* items → List\<BottomNavigationBarItem>：描述选项卡的样式和个数。
	* onTap → ValueChanged\<int>?：选项卡被点击的回调。

### CupertinoTabScaffold —— Cupertino设计风格的选项卡页面脚手架

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > CupertinoTabScaffold
* 构造函数
	```dart
	CupertinoTabScaffold({
		Key? key,
		required CupertinoTabBar tabBar,
		required IndexedWidgetBuilder tabBuilder,
		CupertinoTabController? controller,
		Color? backgroundColor,
		bool resizeToAvoidBottomInset = true,
		String? restorationId
	})
	```
* 属性
	* tabBar → CupertinoTabBar：选项卡栏。
	* tabBuilder → IndexedWidgetBuilder：选项卡页面内容构建器。

### CupertinoTabView —— Cupertino设计风格的选项卡页面内容视图

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > CupertinoTabView
* 构造函数
	```dart
	CupertinoTabView({
		Key? key,
		WidgetBuilder? builder,
		GlobalKey<NavigatorState>? navigatorKey,
		String? defaultTitle,
		Map<String,
		WidgetBuilder>? routes,
		RouteFactory? onGenerateRoute,
		RouteFactory? onUnknownRoute,
		List<NavigatorObserver> navigatorObservers = const <NavigatorObserver>[],
		String? restorationScopeId
	})
	```
* builder → WidgetBuilder?：内容构建器。

### CupertinoTimerPicker —— Cupertino设计风格的定时器选择器

可选范围是0至23小时59分59秒。

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > CupertinoTimerPicker
* 构造函数
	```dart
	CupertinoTimerPicker({
		Key? key,
		CupertinoTimerPickerMode mode = CupertinoTimerPickerMode.hms,
		Duration initialTimerDuration = Duration.zero,
		int minuteInterval = 1,
		int secondInterval = 1,
		AlignmentGeometry alignment = Alignment.center,
		Color? backgroundColor,
		double itemExtent = _kItemExtent,
		required ValueChanged<Duration> onTimerDurationChanged,
		SelectionOverlayBuilder? selectionOverlayBuilder
	})
	```
* 属性
	* initialTimerDuration → Duration：初始定时值。
	* mode → CupertinoTimerPickerMode：定时器模式。
	* onTimerDurationChanged → ValueChanged\<Duration>：定时值改变时的回调。

## 枚举

### CupertinoTimerPickerMode —— Cupertino设计风格的定时器选择器模式

* hm → const CupertinoTimerPickerMode：时分。
* ms → const CupertinoTimerPickerMode：分秒。
* hms → const CupertinoTimerPickerMode：时分秒。

## 函数

### showCupertinoDialog —— 展示Cupertino设计风格的对话框

```dart
Future<T?> showCupertinoDialog<T>({
	required BuildContext context,
	required WidgetBuilder builder,
	String? barrierLabel,
	bool useRootNavigator = true,
	bool barrierDismissible = false,
	RouteSettings? routeSettings,
	Offset? anchorPoint,
})
```

### showCupertinoModalPopup\<T> —— 展示Cupertino设计风格的模态弹出对话框

```dart
Future<T?> showCupertinoModalPopup<T>({
	required BuildContext context,
	required WidgetBuilder builder,
	ImageFilter? filter,
	Color barrierColor = kCupertinoModalBarrierColor,
	bool barrierDismissible = true,
	bool useRootNavigator = true,
	bool semanticsDismissible = false,
	RouteSettings? routeSettings,
	Offset? anchorPoint,
})
```

# driver_extension 【驱动扩展库】

## 函数

### enableFlutterDriverExtension —— 启用Flutter驱动扩展

```dart
void enableFlutterDriverExtension({
	DataHandler? handler,
	bool silenceErrors = false,
	bool enableTextEntryEmulation = true,
	List<FinderExtension>? finders,
	List<CommandExtension>? commands,
})
```

# flutter_driver 【驱动库】

## 类

### CommonFinders —— 普通查找器

* 方法
	* byValueKey(dynamic key) → SerializableFinder

### FlutterDriver —— Flutter驱动

* 构造函数
	```dart
	FlutterDriver.connectedTo({FlutterWebConnection? webConnection, VmService? serviceClient, Isolate? appIsolate})
	```
* 方法
	* close() → Future\<void>
	* getText(SerializableFinder finder, {Duration? timeout}) → Future\<String>
	* tap(SerializableFinder finder, {Duration? timeout}) → Future\<void>

# flutter_test 【测试库】

## 类

### CommonFinders —— 普通查找器

* 方法
	* byIcon(IconData icon, {bool skipOffstage = true}) → Finder
	* text(String text, {bool findRichText = false, bool skipOffstage = true}) → Finder

### WidgetTester —— 组件测试类

* 方法
	* pump([Duration? duration, EnginePhase phase = EnginePhase.sendSemanticsUpdate]) → Future\<void>：重建组件。
	* pumpWidget(Widget widget, {Duration? duration, EnginePhase phase = EnginePhase.sendSemanticsUpdate, bool wrapWithView = true}) → Future\<void>：在后台创建组件。
	* tap(FinderBase\<Element> finder, {int? pointer, int buttons = kPrimaryButton, bool warnIfMissed = true, PointerDeviceKind kind = PointerDeviceKind.touch}) → Future\<void>：模拟点击。

## 常量

### find —— 查找器

find → const CommonFinders

### findsNothing —— 查找不到组件

findsNothing → const Matcher

### findsOneWidget —— 查找到一个组件

findsOneWidget → const Matcher

## 函数

### expect —— 测试结果期望

```dart
void expect(
	dynamic actual,
	dynamic matcher, {
	String? reason,
	dynamic skip,
})
```

### group —— 注册测试组

```dart
@isTestGroup
void group(
	Object description,
	void body(), {
	dynamic skip,
	int? retry,
})
```

### setUpAll —— 注册在所有测试执行前的动作

```dart
void setUpAll( dynamic body() )
```

### tearDownAll —— 注册在所有测试执行后的动作

```dart
void tearDownAll( dynamic body() )
```

### test —— 注册单元测试

```dart
@isTest
void test(
	Object description,
	dynamic body(), {
	String? testOn,
	Timeout? timeout,
	dynamic skip,
	dynamic tags,
	Map<String, dynamic>? onPlatform,
	int? retry,
})
```

### testWidgets —— 注册组件测试

```dart
@isTest
void testWidgets(
	String description,
	WidgetTesterCallback callback, {
	bool? skip,
	Timeout? timeout,
	bool semanticsEnabled = true,
	TestVariant<Object?> variant = const DefaultTestVariant(),
	dynamic tags,
	int? retry,
	LeakTesting? experimentalLeakTesting,
})
```

# foundation 【基础库】

自动导入。

## 属性

### debugPrint —— 调试打印

```dart
debugPrint ↔ DebugPrintCallback
```

# gestures 【手势库】

```dart
import 'package:flutter/gestures.dart';
```

## 类

### GestureArenaMember —— 手势竞技员

* 构造函数
	```dart
	GestureArenaMember()
	```

### GestureRecognizer —— 手势识别器

内部结合`Listener`实现。

* 继承
	Object > GestureArenaMember > GestureRecognizer
* 构造函数
	```dart
	GestureRecognizer({
		Object? debugOwner,
		Set<PointerDeviceKind>? supportedDevices,
		AllowedButtonsFilter allowedButtonsFilter = _defaultButtonAcceptBehavior
	})
	```

### PointerEvent —— 指向事件

* 属性
	* orientation → double：移动方向。
	* pressure → double：压力。

# physics 【物理效果库】

```dart
import 'package:flutter/physics.dart';
```

此库是对animation库的再封装。

# material 【Material（Android）风格库】

```dart
import 'package:flutter/material.dart';
```

## 类

### AboutListTile —— 展示“关于”信息的列表瓦片

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > AboutListTile
* 构造函数
	```dart
	AboutListTile({
		Key? key,
		Widget? icon,
		Widget? child,
		String? applicationName,
		String? applicationVersion,
		Widget? applicationIcon,
		String? applicationLegalese,
		List<Widget>? aboutBoxChildren,
		bool? dense
	})
	```

### AlertDialog —— 提示框

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > AlertDialog
* 构造函数
	```dart
	AlertDialog({
		Key? key,
		Widget? icon,
		EdgeInsetsGeometry? iconPadding,
		Color? iconColor,
		Widget? title,
		EdgeInsetsGeometry? titlePadding,
		TextStyle? titleTextStyle,
		Widget? content,
		EdgeInsetsGeometry? contentPadding,
		TextStyle? contentTextStyle,
		List<Widget>? actions,
		EdgeInsetsGeometry? actionsPadding,
		MainAxisAlignment? actionsAlignment,
		OverflowBarAlignment? actionsOverflowAlignment,
		VerticalDirection? actionsOverflowDirection,
		double? actionsOverflowButtonSpacing,
		EdgeInsetsGeometry? buttonPadding,
		Color? backgroundColor,
		double? elevation,
		Color? shadowColor,
		Color? surfaceTintColor,
		String? semanticLabel,
		EdgeInsets? insetPadding,
		Clip? clipBehavior,
		ShapeBorder? shape,
		AlignmentGeometry? alignment,
		bool scrollable = false
	})
	```
* 属性
	* actions → List\<Widget>?：操作列表。总是横向排列。
	* content → Widget?：提示内容。
	* title → Widget?：标题。

### AppBar —— 应用程序栏

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > AppBar
* 构造函数
	```dart
	AppBar({
		Key? key,
		Widget? leading,
		bool automaticallyImplyLeading = true,
		Widget? title,
		List<Widget>? actions,
		Widget? flexibleSpace,
		PreferredSizeWidget? bottom,
		double? elevation,
		double? scrolledUnderElevation,
		ScrollNotificationPredicate notificationPredicate = defaultScrollNotificationPredicate,
		Color? shadowColor,
		Color? surfaceTintColor,
		ShapeBorder? shape,
		Color? backgroundColor,
		Color? foregroundColor,
		IconThemeData? iconTheme,
		IconThemeData? actionsIconTheme,
		bool primary = true,
		bool? centerTitle,
		bool excludeHeaderSemantics = false,
		double? titleSpacing,
		double toolbarOpacity = 1.0,
		double bottomOpacity = 1.0,
		double? toolbarHeight,
		double? leadingWidth,
		TextStyle? toolbarTextStyle,
		TextStyle? titleTextStyle,
		SystemUiOverlayStyle? systemOverlayStyle,
		bool forceMaterialTransparency = false,
		Clip? clipBehavior
	})
	```
* 属性
	* actions → List\<Widget>?：标题之后的组件列表。
	* bottom → PreferredSizeWidget?：底部组件。
	* centerTitle → bool?：标题是否居中。如为false，则靠近起点位置。
	* leading → Widget?：左上角组件。位于标题之前。
	* title → Widget?：标题。

### BottomNavigationBar —— 底部导航栏

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > BottomNavigationBar
* 构造函数
	```dart
	BottomNavigationBar({
		Key? key,
		required List<BottomNavigationBarItem> items,
		ValueChanged<int>? onTap,
		int currentIndex = 0,
		double? elevation,
		BottomNavigationBarType? type,
		Color? fixedColor,
		Color? backgroundColor,
		double iconSize = 24.0,
		Color? selectedItemColor,
		Color? unselectedItemColor,
		IconThemeData? selectedIconTheme,
		IconThemeData? unselectedIconTheme,
		double selectedFontSize = 14.0,
		double unselectedFontSize = 12.0,
		TextStyle? selectedLabelStyle,
		TextStyle? unselectedLabelStyle,
		bool? showSelectedLabels,
		bool? showUnselectedLabels,
		MouseCursor? mouseCursor,
		bool? enableFeedback,
		BottomNavigationBarLandscapeLayout? landscapeLayout,
		bool useLegacyColorScheme = true
	})
	```
* 属性
	* currentIndex → int：选中选项卡的下标。
	* items → List\<BottomNavigationBarItem>：描述选项卡的样式和个数。
	* onTap → ValueChanged\<int>?：选项卡被点击的回调。

### Card —— 卡片

内容不允许滚动。

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > Card
* 构造函数
	```dart
	Card({
		Key? key,
		Color? color,
		Color? shadowColor,
		Color? surfaceTintColor,
		double? elevation,
		ShapeBorder? shape,
		bool borderOnForeground = true,
		EdgeInsetsGeometry? margin,
		Clip? clipBehavior,
		Widget? child,
		bool semanticContainer = true
	})
	```
* 属性
	* child → Widget?：子组件。
	* shape → ShapeBorder?：形状。

### Checkbox —— 复选框

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > Checkbox
* 构造函数
	```dart
	Checkbox({
		Key? key,
		required bool? value,
		bool tristate = false,
		required ValueChanged<bool?>? onChanged,
		MouseCursor? mouseCursor,
		Color? activeColor,
		MaterialStateProperty<Color?>? fillColor,
		Color? checkColor,
		Color? focusColor,
		Color? hoverColor,
		MaterialStateProperty<Color?>? overlayColor,
		double? splashRadius,
		MaterialTapTargetSize? materialTapTargetSize,
		VisualDensity? visualDensity,
		FocusNode? focusNode,
		bool autofocus = false,
		OutlinedBorder? shape, 
		BorderSide? side,
		bool isError = false,
		String? semanticLabel
	})
	```

### Chip —— 碎片/标签

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > Chip
* 构造函数
	```dart
	Chip({
		Key? key,
		Widget? avatar,
		required Widget label,
		TextStyle? labelStyle,
		EdgeInsetsGeometry? labelPadding,
		Widget? deleteIcon,
		VoidCallback? onDeleted,
		Color? deleteIconColor,
		String? deleteButtonTooltipMessage,
		BorderSide? side,
		OutlinedBorder? shape,
		Clip clipBehavior = Clip.none, 
		FocusNode? focusNode,
		bool autofocus = false,
		MaterialStateProperty<Color?>? color,
		Color? backgroundColor,
		EdgeInsetsGeometry? padding,
		VisualDensity? visualDensity,
		MaterialTapTargetSize? materialTapTargetSize,
		double? elevation,
		Color? shadowColor,
		Color? surfaceTintColor,
		IconThemeData? iconTheme,
		BoxConstraints? avatarBoxConstraints,
		BoxConstraints? deleteIconBoxConstraints,
		ChipAnimationStyle? chipAnimationStyle
	})
	```
* 属性
	* avatar → Widget?：主体内容之前的小图标。
	* backgroundColor → Color?：背景色。
	* deleteButtonTooltipMessage → String?：当删除图标长按时的提示内容。
	* deleteIcon → Widget?：右侧删除图标。
	* deleteIconColor → Color?：右侧删除图标的颜色。
	* label → Widget：主体内容。
	* onDeleted → VoidCallback?：被删除时的回调。
	* shape → OutlinedBorder?：形状。

### CircleAvatar —— 圆形头像

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > CircleAvatar
* 构造函数
	```dart
	CircleAvatar({
		Key? key,
		Widget? child,
		Color? backgroundColor,
		ImageProvider<Object>? backgroundImage,
		ImageProvider<Object>? foregroundImage,
		ImageErrorListener? onBackgroundImageError,
		ImageErrorListener? onForegroundImageError,
		Color? foregroundColor,
		double? radius,
		double? minRadius,
		double? maxRadius
	})
	```

### CircularProgressIndicator —— 圆形进度条

* 继承
	Object > DiagnosticableTree > Widget StatefulWidget > ProgressIndicator > CircularProgressIndicator
* 构造函数
	```dart
	CircularProgressIndicator({
		Key? key,
		double? value,
		Color? backgroundColor,
		Color? color,
		Animation<Color?>? valueColor,
		double strokeWidth = 4.0,
		double strokeAlign = strokeAlignCenter,
		String? semanticsLabel,
		String? semanticsValue,
		StrokeCap? strokeCap
	})
	```
* 属性
	* value → double?：当前进度值。范围从0到1。如为null，则只显示动画效果。

### Colors —— 颜色集

### Divider —— 分隔线

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > Divider
* 构造函数
	```dart
	Divider({
		Key? key,
		double? height,
		double? thickness,
		double? indent,
		double? endIndent,
		Color? color
	})
	```

### Drawer —— 抽屉

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > Drawer
* 构造函数
	```dart
	Drawer({
		Key? key,
		Color? backgroundColor,
		double? elevation,
		Color? shadowColor,
		Color? surfaceTintColor,
		ShapeBorder? shape,
		double? width,
		Widget? child,
		String? semanticLabel,
		Clip? clipBehavior
	})
	```

### DrawerHeader —— 抽屉头

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > DrawerHeader
* 构造函数
	```dart
	DrawerHeader({
		Key? key,
		Decoration? decoration,
		EdgeInsetsGeometry? margin = const EdgeInsets.only(bottom: 8.0),
		EdgeInsetsGeometry padding = const EdgeInsets.fromLTRB(16.0, 16.0, 16.0, 8.0),
		Duration duration = const Duration(milliseconds: 250),
		Curve curve = Curves.fastOutSlowIn,
		required Widget? child
	})
	```

### ElevatedButton —— 突起按钮

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > ButtonStyleButton > ElevatedButton
* 构造函数
	```dart
	ElevatedButton({
		Key? key,
		required VoidCallback? onPressed,
		VoidCallback? onLongPress,
		ValueChanged<bool>? onHover,
		ValueChanged<bool>? onFocusChange,
		ButtonStyle? style,
		FocusNode? focusNode,
		bool autofocus = false,
		Clip? clipBehavior,
		MaterialStatesController? statesController,
		required Widget? child,
		IconAlignment iconAlignment = IconAlignment.start
	})
	```

### ExpansionPanel —— 可展开面板

* 构造函数
	```dart
	ExpansionPanel({
		required ExpansionPanelHeaderBuilder headerBuilder,
		required Widget body,
		bool isExpanded = false,
		bool canTapOnHeader = false,
		Color? backgroundColor,
		Color? splashColor,
		Color? highlightColor
	})
	```
* 属性
	* body → Widget：展开的内容。
	* headerBuilder → ExpansionPanelHeaderBuilder：标头构建器。
	* isExpanded → bool：是否展开。

### ExpansionPanelList —— 可展开面板列表

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > ExpansionPanelList
* 构造函数
	```dart
	ExpansionPanelList({
		Key? key,
		List<ExpansionPanel> children = const <ExpansionPanel>[],
		ExpansionPanelCallback? expansionCallback,
		Duration animationDuration = kThemeAnimationDuration,
		EdgeInsets expandedHeaderPadding = _kPanelHeaderExpandedDefaultPadding,
		Color? dividerColor,
		double elevation = 2,
		Color? expandIconColor,
		double materialGapSize = 16.0
	})
	```
* 属性
	* children → List\<ExpansionPanel>：子组件列表。
	* expansionCallback → ExpansionPanelCallback?：每个子组件展开/缩起时的回调。

### FloatingActionButton —— 浮动操作按钮

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > FloatingActionButton
* 构造函数
	```dart
	FloatingActionButton({
		Key? key,
		Widget? child,
		String? tooltip,
		Color? foregroundColor,
		Color? backgroundColor,
		Color? focusColor,
		Color? hoverColor,
		Color? splashColor,
		Object? heroTag = const _DefaultHeroTag(),
		double? elevation,
		double? focusElevation,
		double? hoverElevation,
		double? highlightElevation,
		double? disabledElevation,
		required VoidCallback? onPressed,
		MouseCursor? mouseCursor,
		bool mini = false,
		ShapeBorder? shape,
		Clip clipBehavior = Clip.none,
		FocusNode? focusNode,
		bool autofocus = false,
		MaterialTapTargetSize? materialTapTargetSize,
		bool isExtended = false,
		bool? enableFeedback
	})
	```

	```dart
	FloatingActionButton.extended({
		Key? key,
		String? tooltip,
		Color? foregroundColor,
		Color? backgroundColor,
		Color? focusColor,
		Color? hoverColor,
		Object? heroTag = const _DefaultHeroTag(),
		double? elevation,
		double? focusElevation,
		double? hoverElevation,
		Color? splashColor,
		double? highlightElevation,
		double? disabledElevation,
		required VoidCallback? onPressed,
		MouseCursor? mouseCursor = SystemMouseCursors.click,
		ShapeBorder? shape,
		bool isExtended = true,
		MaterialTapTargetSize? materialTapTargetSize,
		Clip clipBehavior = Clip.none,
		FocusNode? focusNode,
		bool autofocus = false,
		double? extendedIconLabelSpacing,
		EdgeInsetsGeometry? extendedPadding,
		TextStyle? extendedTextStyle,
		Widget? icon,
		required Widget label,
		bool? enableFeedback
	})
	```
* 属性
	* child → Widget?：子组件。
	* heroTag → Object?：共享元素过渡动画所使用的标签。
	* mini → bool：控制按钮的大小。
	* isExtended → bool：是否是拉长的按钮。
	* onPressed → VoidCallback?：点击回调。

### MaterialApp —— Material设计风格APP

必需由`MaterialApp`包装Material设计风格的组件。

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > MaterialApp
* 构造函数
	```dart
	MaterialApp({
		Key? key,
		GlobalKey<NavigatorState>? navigatorKey,
		GlobalKey<ScaffoldMessengerState>? scaffoldMessengerKey,
		Widget? home,
		Map<String,
		WidgetBuilder> routes = const <String, WidgetBuilder>{},
		String? initialRoute,
		RouteFactory? onGenerateRoute,
		InitialRouteListFactory? onGenerateInitialRoutes,
		RouteFactory? onUnknownRoute,
		NotificationListenerCallback<NavigationNotification>? onNavigationNotification,
		List<NavigatorObserver> navigatorObservers = const <NavigatorObserver>[],
		TransitionBuilder? builder,
		String title = '',
		GenerateAppTitle? onGenerateTitle,
		Color? color,
		ThemeData? theme,
		ThemeData? darkTheme,
		ThemeData? highContrastTheme,
		ThemeData? highContrastDarkTheme,
		ThemeMode? themeMode = ThemeMode.system,
		Duration themeAnimationDuration = kThemeAnimationDuration,
		Curve themeAnimationCurve = Curves.linear,
		Locale? locale,
		Iterable<LocalizationsDelegate>? localizationsDelegates,
		LocaleListResolutionCallback? localeListResolutionCallback,
		LocaleResolutionCallback? localeResolutionCallback,
		Iterable<Locale> supportedLocales = const <Locale>[Locale('en', 'US')],
		bool debugShowMaterialGrid = false,
		bool showPerformanceOverlay = false,
		bool checkerboardRasterCacheImages = false,
		bool checkerboardOffscreenLayers = false,
		bool showSemanticsDebugger = false,
		bool debugShowCheckedModeBanner = true,
		Map<ShortcutActivator, Intent>? shortcuts,
		Map<Type, Action<Intent>>? actions,
		String? restorationScopeId,
		ScrollBehavior? scrollBehavior,
		@Deprecated(
			'Remove this parameter as it is now ignored. '
			'MaterialApp never introduces its own MediaQuery; the View widget takes care of that. '
			'This feature was deprecated after v3.7.0-29.0.pre.'
		)
		bool useInheritedMediaQuery = false,
		AnimationStyle? themeAnimationStyle
	})
	```

	* theme：APP的主题。
* 属性
	* color → Color?：应用图标的颜色，显示在系统的最近任务视图中。
	* debugShowCheckedModeBanner → bool：调试模式下是否显示右上角的“DEBUG”条幅。发布模式下忽略此字段，总是不显示。
	* debugShowMaterialGrid → bool：是否显示基线网格。
	* home → Widget?：APP主界面。
	* localeListResolutionCallback → LocaleListResolutionCallback?：选择语系区域时的回调。
	* localizationsDelegates → Iterable\<LocalizationsDelegate>?：多语言本地化代理列表。
	* onGenerateTitle → GenerateAppTitle?
	* routes → Map\<String, WidgetBuilder>?：页面跳转静态定义路由。
	* showPerformanceOverlay → bool：是否显示性能图表。
	* showSemanticsDebugger → bool：是否启用语义调试。
	* supportedLocales → Iterable\<Locale>：支持的语系区域列表。
	* title → String：APP的标题。通常显示在系统的最近任务视图中。

### MaterialButton —— Material设计风格按钮

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > MaterialButton
* 构造函数
	```dart
	MaterialButton({
		Key? key,
		required VoidCallback? onPressed,
		VoidCallback? onLongPress,
		ValueChanged<bool>? onHighlightChanged,
		MouseCursor? mouseCursor,
		ButtonTextTheme? textTheme,
		Color? textColor,
		Color? disabledTextColor,
		Color? color,
		Color? disabledColor,
		Color? focusColor,
		Color? hoverColor,
		Color? highlightColor,
		Color? splashColor,
		Brightness? colorBrightness,
		double? elevation,
		double? focusElevation,
		double? hoverElevation,
		double? highlightElevation,
		double? disabledElevation,
		EdgeInsetsGeometry? padding,
		VisualDensity? visualDensity,
		ShapeBorder? shape,
		Clip clipBehavior = Clip.none,
		FocusNode? focusNode,
		bool autofocus = false,
		MaterialTapTargetSize? materialTapTargetSize,
		Duration? animationDuration,
		double? minWidth,
		double? height,
		bool enableFeedback = true,
		Widget? child
	})
	```

### MaterialPageRoute\<T> —— Material设计风格页面的路由

* 继承
	Object > Route\<T> > OverlayRoute\<T> > TransitionRoute\<T> > ModalRoute\<T> > PageRoute\<T> > MaterialPageRoute
* 构造函数
	```dart
	MaterialPageRoute({
		required WidgetBuilder builder,
		RouteSettings? settings,
		bool? requestFocus,
		bool maintainState = true,
		bool fullscreenDialog = false,
		bool allowSnapshotting = true,
		bool barrierDismissible = false
	})
	```

### IconButton —— 图标按钮

点击时产生圆形背景和水波纹效果。

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > IconButton
* 构造函数
	```dart
	IconButton({
		Key? key,
		double? iconSize,
		VisualDensity? visualDensity,
		EdgeInsetsGeometry? padding,
		AlignmentGeometry? alignment,
		double? splashRadius,
		Color? color,
		Color? focusColor,
		Color? hoverColor,
		Color? highlightColor,
		Color? splashColor,
		Color? disabledColor,
		required VoidCallback? onPressed,
		MouseCursor? mouseCursor,
		FocusNode? focusNode,
		bool autofocus = false,
		String? tooltip,
		bool? enableFeedback,
		BoxConstraints? constraints,
		ButtonStyle? style,
		bool? isSelected,
		Widget? selectedIcon,
		required Widget icon
	})
	```
* 属性
	* disabledColor → Color?
	* highlightColor → Color?
	* icon → Widget：图标。
	* iconSize → double?：图标大小。
	* onPressed → VoidCallback?：点击回调。

### Icons —— 图标集

* 常量
	* phone → const IconData：电话图标。

### InkWell —— 水波纹

点击产生水波纹效果。

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > InkResponse > InkWell
* 构造函数
	```dart
	InkWell({
		Key? key,
		Widget? child,
		GestureTapCallback? onTap,
		GestureTapCallback? onDoubleTap,
		GestureLongPressCallback? onLongPress,
		GestureTapDownCallback? onTapDown,
		GestureTapUpCallback? onTapUp,
		GestureTapCallback? onTapCancel,
		GestureTapCallback? onSecondaryTap,
		GestureTapUpCallback? onSecondaryTapUp,
		GestureTapDownCallback? onSecondaryTapDown,
		GestureTapCallback? onSecondaryTapCancel,
		ValueChanged<bool>? onHighlightChanged,
		ValueChanged<bool>? onHover,
		MouseCursor? mouseCursor,
		Color? focusColor,
		Color? hoverColor,
		Color? highlightColor,
		MaterialStateProperty<Color?>? overlayColor,
		Color? splashColor,
		InteractiveInkFeatureFactory? splashFactory,
		double? radius,
		BorderRadius? borderRadius,
		ShapeBorder? customBorder,
		bool? enableFeedback = true,
		bool excludeFromSemantics = false,
		FocusNode? focusNode,
		bool canRequestFocus = true,
		ValueChanged<bool>? onFocusChange,
		bool autofocus = false,
		MaterialStatesController? statesController,
		Duration? hoverDuration
	})
	```

### InputDecoration —— 输入装饰

* 构造函数
	```dart
	InputDecoration({
		Widget? icon,
		Color? iconColor,
		Widget? label,
		String? labelText,
		TextStyle? labelStyle,
		TextStyle? floatingLabelStyle,
		Widget? helper,
		String? helperText,
		TextStyle? helperStyle,
		int? helperMaxLines,
		String? hintText,
		TextStyle? hintStyle,
		TextDirection? hintTextDirection,
		int? hintMaxLines,
		Duration? hintFadeDuration,
		Widget? error,
		String? errorText,
		TextStyle? errorStyle,
		int? errorMaxLines,
		FloatingLabelBehavior? floatingLabelBehavior,
		FloatingLabelAlignment? floatingLabelAlignment,
		bool? isCollapsed,
		bool? isDense,
		EdgeInsetsGeometry? contentPadding,
		Widget? prefixIcon,
		BoxConstraints? prefixIconConstraints,
		Widget? prefix,
		String? prefixText,
		TextStyle? prefixStyle,
		Color? prefixIconColor,
		Widget? suffixIcon,
		Widget? suffix,
		String? suffixText,
		TextStyle? suffixStyle,
		Color? suffixIconColor,
		BoxConstraints? suffixIconConstraints,
		Widget? counter,
		String? counterText,
		TextStyle? counterStyle,
		bool? filled,
		Color? fillColor,
		Color? focusColor,
		Color? hoverColor,
		InputBorder? errorBorder,
		InputBorder? focusedBorder,
		InputBorder? focusedErrorBorder,
		InputBorder? disabledBorder,
		InputBorder? enabledBorder,
		InputBorder? border,
		bool enabled = true,
		String? semanticCounterText,
		bool? alignLabelWithHint,
		BoxConstraints? constraints
	})
	```
* 属性
	* hintText → String?：提示文本。
	* labelText → String?：标题文本。
	* prefixIcon → Widget?：首部图标。

### LinearProgressIndicator —— 水平进度条

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > ProgressIndicator > LinearProgressIndicator
* 构造函数
	```dart
	LinearProgressIndicator({
		Key? key,
		double? value,
		Color? backgroundColor,
		Color? color,
		Animation<Color?>? valueColor,
		double? minHeight,
		String? semanticsLabel,
		String? semanticsValue,
		BorderRadiusGeometry borderRadius = BorderRadius.zero
	})
	```
* 属性
	* value → double?：当前进度值。范围从0到1。如为null，则只显示动画效果。

### ListTile —— 列表瓦片

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > ListTile
* 构造函数
	```dart
	ListTile({
		Key? key,
		Widget? leading,
		Widget? title,
		Widget? subtitle,
		Widget? trailing,
		bool isThreeLine = false,
		bool? dense,
		VisualDensity? visualDensity,
		ShapeBorder? shape,
		ListTileStyle? style,
		Color? selectedColor,
		Color? iconColor,
		Color? textColor,
		TextStyle? titleTextStyle,
		TextStyle? subtitleTextStyle,
		TextStyle? leadingAndTrailingTextStyle,
		EdgeInsetsGeometry? contentPadding,
		bool enabled = true,
		GestureTapCallback? onTap,
		GestureLongPressCallback? onLongPress,
		ValueChanged<bool>? onFocusChange,
		MouseCursor? mouseCursor,
		bool selected = false,
		Color? focusColor,
		Color? hoverColor,
		Color? splashColor,
		FocusNode? focusNode,
		bool autofocus = false,
		Color? tileColor,
		Color? selectedTileColor,
		bool? enableFeedback,
		double? horizontalTitleGap,
		double? minVerticalPadding,
		double? minLeadingWidth,
		double? minTileHeight,
		ListTileTitleAlignment? titleAlignment
	})
	```

### OutlinedButton —— 线框按钮

未点击时有浅浅的边框，背景透明；点击时边框亮起，显示背景色并伴有水波纹扩散。

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > ButtonStyleButton > OutlinedButton
* 构造函数
	```dart
	OutlinedButton({
		Key? key,
		required VoidCallback? onPressed,
		VoidCallback? onLongPress,
		ValueChanged<bool>? onHover,
		ValueChanged<bool>? onFocusChange,
		ButtonStyle? style,
		FocusNode? focusNode,
		bool autofocus = false,
		Clip? clipBehavior,
		MaterialStatesController? statesController,
		required Widget? child,
		IconAlignment iconAlignment = IconAlignment.start
	})
	```

### PopupMenuButton\<T> —— 弹出式菜单按钮

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > PopupMenuButton
* 构造函数
	```dart
	PopupMenuButton({
		Key? key,
		required PopupMenuItemBuilder<T> itemBuilder,
		T? initialValue,
		VoidCallback? onOpened,
		PopupMenuItemSelected<T>? onSelected,
		PopupMenuCanceled? onCanceled,
		String? tooltip,
		double? elevation,
		Color? shadowColor,
		Color? surfaceTintColor,
		EdgeInsetsGeometry padding = const EdgeInsets.all(8.0),
		EdgeInsetsGeometry? menuPadding,
		Widget? child,
		double? splashRadius,
		Widget? icon,
		double? iconSize,
		Offset offset = Offset.zero,
		bool enabled = true,
		ShapeBorder? shape,
		Color? color,
		Color? iconColor,
		bool? enableFeedback,
		BoxConstraints? constraints,
		PopupMenuPosition? position,
		Clip clipBehavior = Clip.none,
		bool useRootNavigator = false,
		AnimationStyle? popUpAnimationStyle,
		RouteSettings? routeSettings,
		ButtonStyle? style
	})
	```
* 属性
	* child → Widget?：子组件。
	* itemBuilder → PopupMenuItemBuilder\<T>：弹出式菜单项构建器。
	* onCanceled → PopupMenuCanceled?：弹出式菜单关闭时的回调。只在没有选中菜单项时运行。
	* onSelected → PopupMenuItemSelected\<T>?：菜单项被选中后的回调。

### PopupMenuItem\<T> —— 弹出式菜单项

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > PopupMenuEntry\<T> > PopupMenuItem
* 构造函数
	```dart
	PopupMenuItem({
		Key? key,
		T? value,
		VoidCallback? onTap,
		bool enabled = true,
		double height = kMinInteractiveDimension,
		EdgeInsets? padding,
		TextStyle? textStyle,
		MaterialStateProperty<TextStyle?>? labelTextStyle,
		MouseCursor? mouseCursor,
		required Widget? child
	})
	```

### Radio\<T> —— 单选框

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > Radio
* 构造函数
	```dart
	Radio({
		Key? key,
		required T value,
		required T? groupValue,
		required ValueChanged<T?>? onChanged,
		MouseCursor? mouseCursor,
		bool toggleable = false,
		Color? activeColor,
		MaterialStateProperty<Color?>? fillColor,
		Color? focusColor,
		Color? hoverColor,
		MaterialStateProperty<Color?>? overlayColor,
		double? splashRadius,
		MaterialTapTargetSize? materialTapTargetSize,
		VisualDensity? visualDensity,
		FocusNode? focusNode,
		bool autofocus = false
	})
	```

### Scaffold —— 脚手架

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > Scaffold
* 构造函数
	```dart
	Scaffold({
		Key? key,
		PreferredSizeWidget? appBar,
		Widget? body,
		Widget? floatingActionButton,
		FloatingActionButtonLocation? floatingActionButtonLocation,
		FloatingActionButtonAnimator? floatingActionButtonAnimator,
		List<Widget>? persistentFooterButtons,
		AlignmentDirectional persistentFooterAlignment = AlignmentDirectional.centerEnd,
		Widget? drawer,
		DrawerCallback? onDrawerChanged,
		Widget? endDrawer,
		DrawerCallback? onEndDrawerChanged,
		Widget? bottomNavigationBar,
		Widget? bottomSheet,
		Color? backgroundColor,
		bool? resizeToAvoidBottomInset,
		bool primary = true,
		DragStartBehavior drawerDragStartBehavior = DragStartBehavior.start,
		bool extendBody = false,
		bool extendBodyBehindAppBar = false,
		Color? drawerScrimColor,
		double? drawerEdgeDragWidth,
		bool drawerEnableOpenDragGesture = true,
		bool endDrawerEnableOpenDragGesture = true,
		String? restorationId
	})
	```
* 属性
	* appBar → PreferredSizeWidget?：顶部应用程序栏。由一个标题栏和其它可能的组件组成。
	* body → Widget?：界面主要内容。
	* bottomNavigationBar → Widget?：底部导航栏。
	* drawer → Widget?：从左侧滑出的抽屉组件。
	* floatingActionButton → Widget?：浮动操作按钮。Z轴它位于整个界面之上。
	* floatingActionButtonLocation → FloatingActionButtonLocation?：浮动操作按钮的位置。默认在界面右下角。

### ScaffoldMessenger —— 脚手架信使

* 静态方法
	* of(BuildContext context) → ScaffoldMessengerState

### ScaffoldMessengerState —— 脚手架信使状态

* 方法
	* showSnackBar(SnackBar snackBar, {AnimationStyle? snackBarAnimationStyle}) → ScaffoldFeatureController\<SnackBar, SnackBarClosedReason>

### SimpleDialog —— 简单对话框

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > SimpleDialog
* 构造函数
	```dart
	SimpleDialog({
		Key? key,
		Widget? title,
		EdgeInsetsGeometry titlePadding = const EdgeInsets.fromLTRB(24.0, 24.0, 24.0, 0.0),
		TextStyle? titleTextStyle,
		List<Widget>? children,
		EdgeInsetsGeometry contentPadding = const EdgeInsets.fromLTRB(0.0, 12.0, 0.0, 16.0),
		Color? backgroundColor,
		double? elevation,
		Color? shadowColor,
		Color? surfaceTintColor,
		String? semanticLabel,
		EdgeInsets? insetPadding,
		Clip? clipBehavior,
		ShapeBorder? shape,
		AlignmentGeometry? alignment
	})
	```
* 属性
	* children → List\<Widget>?：子组件列表。总是纵向排列。
	* title → Widget?：标题。

### SimpleDialogOption —— 简单对话框的选项

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > SimpleDialogOption
* 构造函数
	```dart
	SimpleDialogOption({
		Key? key,
		VoidCallback? onPressed,
		EdgeInsets? padding,
		Widget? child
	})
	```

### Slider —— 滑块

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > Slider
* 构造函数
	```dart
	Slider({
		Key? key,
		required double value,
		double? secondaryTrackValue,
		required ValueChanged<double>? onChanged,
		ValueChanged<double>? onChangeStart,
		ValueChanged<double>? onChangeEnd,
		double min = 0.0,
		double max = 1.0,
		int? divisions,
		String? label,
		Color? activeColor,
		Color? inactiveColor,
		Color? secondaryActiveColor,
		Color? thumbColor,
		MaterialStateProperty<Color?>? overlayColor,
		MouseCursor? mouseCursor,
		SemanticFormatterCallback? semanticFormatterCallback,
		FocusNode? focusNode,
		bool autofocus = false,
		SliderInteraction? allowedInteraction
	})
	```
* 属性
	* activeColor → Color?：滑过部分的颜色。
	* inactiveColor → Color?：未滑过部分的颜色。
	* max → double：最大位置值。
	* min → double：最小位置值。
	* onChanged → ValueChanged\<double>?：滑动过程中的回调。`Slider`不维护任何状态，需要在此方法中重绘滑块。
	* onChangeEnd → ValueChanged\<double>?：滑动结束的回调。
	* onChangeStart → ValueChanged\<double>?：滑动开始的回调。
	* value → double：当前位置值。

### SnackBar —— 底部提示栏

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > SnackBar
* 构造函数
	```dart
	SnackBar({
		Key? key,
		required Widget content,
		Color? backgroundColor,
		double? elevation,
		EdgeInsetsGeometry? margin,
		EdgeInsetsGeometry? padding,
		double? width,
		ShapeBorder? shape,
		HitTestBehavior? hitTestBehavior,
		SnackBarBehavior? behavior,
		SnackBarAction? action,
		double? actionOverflowThreshold,
		bool? showCloseIcon,
		Color? closeIconColor,
		Duration duration = _snackBarDisplayDuration,
		Animation<double>? animation,
		VoidCallback? onVisible,
		DismissDirection? dismissDirection,
		Clip clipBehavior = Clip.hardEdge
	})
	```
*属性
	* action → SnackBarAction?：用来触发一个快捷操作。
	* backgroundColor → Color?：背景色。默认为深灰色。
	* content → Widget：内容。
	* duration → Duration：显示的持续时长。

### Switch —— 开关

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > Switch
* 构造函数
	```dart
	Switch({
		Key? key,
		required bool value,
		required ValueChanged\<bool>? onChanged,
		Color? activeColor,
		Color? activeTrackColor,
		Color? inactiveThumbColor,
		Color? inactiveTrackColor,
		ImageProvider<Object>? activeThumbImage,
		ImageErrorListener? onActiveThumbImageError,
		ImageProvider<Object>? inactiveThumbImage,
		ImageErrorListener? onInactiveThumbImageError,
		MaterialStateProperty<Color?>? thumbColor,
		MaterialStateProperty<Color?>? trackColor,
		MaterialStateProperty<Color?>? trackOutlineColor,
		MaterialStateProperty<double?>? trackOutlineWidth,
		MaterialStateProperty<Icon?>? thumbIcon,
		MaterialTapTargetSize? materialTapTargetSize,
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		MouseCursor? mouseCursor,
		Color? focusColor,
		Color? hoverColor,
		MaterialStateProperty<Color?>? overlayColor,
		double? splashRadius,
		FocusNode? focusNode,
		ValueChanged<bool>? onFocusChange,
		bool autofocus = false
	})
	```

### Tab —— 选项卡

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > Tab
* 构造函数
	```dart
	Tab({
		Key? key,
		String? text,
		Widget? icon,
		EdgeInsetsGeometry? iconMargin,
		double? height,
		Widget? child
	})
	```

### TabBar —— 选项卡栏

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > TabBar
* 构造函数
	```dart
	TabBar({
		Key? key,
		required List<Widget> tabs,
		TabController? controller,
		bool isScrollable = false,
		EdgeInsetsGeometry? padding,
		Color? indicatorColor,
		bool automaticIndicatorColorAdjustment = true,
		double indicatorWeight = 2.0,
		EdgeInsetsGeometry indicatorPadding = EdgeInsets.zero,
		Decoration? indicator,
		TabBarIndicatorSize? indicatorSize,
		Color? dividerColor,
		double? dividerHeight,
		Color? labelColor,
		TextStyle? labelStyle,
		EdgeInsetsGeometry? labelPadding,
		Color? unselectedLabelColor,
		TextStyle? unselectedLabelStyle,
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		MaterialStateProperty<Color?>? overlayColor,
		MouseCursor? mouseCursor,
		bool? enableFeedback,
		ValueChanged<int>? onTap,
		ScrollPhysics? physics,
		InteractiveInkFeatureFactory? splashFactory,
		BorderRadius? splashBorderRadius,
		TabAlignment? tabAlignment,
		TextScaler? textScaler
	})
	```
* 属性
	* controller → TabController?：选项卡控制器。
	* indicatorColor → Color?：选项卡下方的选中指示器颜色。
	* indicatorSize → TabBarIndicatorSize?：选项卡的宽度。
	* indicatorWeight → double：选项卡下方的选中指示器高度。
	* tabs → List\<Widget>：选项卡列表。

### TabBarView —— 选项卡栏页面内容视图

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > TabBarView
* 构造函数
	```dart
	TabBarView({
		Key? key,
		required List<Widget> children,
		TabController? controller,
		ScrollPhysics? physics,
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		double viewportFraction = 1.0,
		Clip clipBehavior = Clip.hardEdge
	})
	```

### TabController —— 水平选项卡控制器

* 继承
	Object > ChangeNotifier > TabController
* 构造函数
	```dart
	TabController({
		int initialIndex = 0,
		Duration? animationDuration,
		required int length,
		required TickerProvider vsync
	})
	```
* 方法
	* dispose() → void

### TextButton —— 文本按钮

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > ButtonStyleButton > TextButton
* 构造函数
	```dart
	TextButton({
		Key? key,
		required VoidCallback? onPressed,
		VoidCallback? onLongPress,
		ValueChanged<bool>? onHover,
		ValueChanged<bool>? onFocusChange,
		ButtonStyle? style,
		FocusNode? focusNode,
		bool autofocus = false,
		Clip? clipBehavior,
		MaterialStatesController? statesController,
		bool? isSemanticButton = true,
		required Widget child,
		IconAlignment iconAlignment = IconAlignment.start
	})
	```

### TextField —— 文本框字段

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > TextField
* 构造函数
	```dart
	TextField({
		Key? key,
		Object groupId = EditableText,
		TextEditingController? controller,
		FocusNode? focusNode,
		UndoHistoryController? undoController,
		InputDecoration? decoration = const InputDecoration(),
		TextInputType? keyboardType,
		TextInputAction? textInputAction,
		TextCapitalization textCapitalization = TextCapitalization.none,
		TextStyle? style,
		StrutStyle? strutStyle,
		TextAlign textAlign = TextAlign.start,
		TextAlignVertical? textAlignVertical,
		TextDirection? textDirection,
		bool readOnly = false,
		@Deprecated(
			'Use `contextMenuBuilder` instead. '
			'This feature was deprecated after v3.3.0-0.5.pre.'
		)
		ToolbarOptions? toolbarOptions,
		bool? showCursor,
		bool autofocus = false,
		MaterialStatesController? statesController,
		String obscuringCharacter = '•',
		bool obscureText = false,
		bool autocorrect = true,
		SmartDashesType? smartDashesType,
		SmartQuotesType? smartQuotesType,
		bool enableSuggestions = true,
		int? maxLines = 1,
		int? minLines,
		bool expands = false,
		int? maxLength,
		MaxLengthEnforcement? maxLengthEnforcement,
		ValueChanged<String>? onChanged,
		VoidCallback? onEditingComplete,
		ValueChanged<String>? onSubmitted,
		AppPrivateCommandCallback? onAppPrivateCommand,
		List<TextInputFormatter>? inputFormatters,
		bool? enabled,
		bool? ignorePointers,
		double cursorWidth = 2.0,
		double? cursorHeight,
		Radius? cursorRadius,
		bool? cursorOpacityAnimates,
		Color? cursorColor,
		Color? cursorErrorColor,
		BoxHeightStyle selectionHeightStyle = ui.BoxHeightStyle.tight,
		BoxWidthStyle selectionWidthStyle = ui.BoxWidthStyle.tight,
		Brightness? keyboardAppearance,
		EdgeInsets scrollPadding = const EdgeInsets.all(20.0),
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		bool? enableInteractiveSelection,
		TextSelectionControls? selectionControls,
		GestureTapCallback? onTap,
		bool onTapAlwaysCalled = false,
		TapRegionCallback? onTapOutside,
		MouseCursor? mouseCursor,
		InputCounterWidgetBuilder? buildCounter,
		ScrollController? scrollController,
		ScrollPhysics? scrollPhysics,
		Iterable<String>? autofillHints = const <String>[],
		ContentInsertionConfiguration? contentInsertionConfiguration,
		Clip clipBehavior = Clip.hardEdge,
		String? restorationId,
		bool scribbleEnabled = true,
		bool enableIMEPersonalizedLearning = true,
		EditableTextContextMenuBuilder? contextMenuBuilder = _defaultContextMenuBuilder,
		bool canRequestFocus = true,
		SpellCheckConfiguration? spellCheckConfiguration,
		TextMagnifierConfiguration? magnifierConfiguration
	})
	```
* 属性
	* autofocus → bool：是否自动获取焦点。
	* controller → TextEditingController?：文本编辑控制器。
	* decoration → InputDecoration?：输入装饰。
	* maxLines → int?：最大行数。默认为1。
	* maxLength → int?：文本最大长度。如指定，则当前长度和最大长度会显示在文本框右下角。
	* maxLengthEnforcement → MaxLengthEnforcement?
	* keyboardType → TextInputType：弹出软键盘的类型。
	* obscureText → bool：是否隐藏文本明文。
	* onChanged → ValueChanged\<String>?：文本内容变更时的回调。
	* onEditingComplete → VoidCallback?：文本编辑完成时的回调。
	* onSubmitted → ValueChanged\<String>?：文本提交时的回调。
	* textInputAction → TextInputAction?：文本输入操作。

### TextFormField —— 表单文本框字段

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > FormField\<String> > TextFormField
* 构造函数
	```dart
	TextFormField({
		Key? key,
		Object groupId = EditableText,
		TextEditingController? controller,
		String? initialValue,
		FocusNode? focusNode,
		String? forceErrorText,
		InputDecoration? decoration = const InputDecoration(),
		TextInputType? keyboardType,
		TextCapitalization textCapitalization = TextCapitalization.none,
		TextInputAction? textInputAction,
		TextStyle? style,
		StrutStyle? strutStyle,
		TextDirection? textDirection,
		TextAlign textAlign = TextAlign.start,
		TextAlignVertical? textAlignVertical,
		bool autofocus = false,
		bool readOnly = false,
		@Deprecated(
			'Use `contextMenuBuilder` instead. '
			'This feature was deprecated after v3.3.0-0.5.pre.'
		)
		ToolbarOptions? toolbarOptions,
		bool? showCursor,
		String obscuringCharacter = '•',
		bool obscureText = false,
		bool autocorrect = true,
		SmartDashesType? smartDashesType,
		SmartQuotesType? smartQuotesType,
		bool enableSuggestions = true,
		MaxLengthEnforcement? maxLengthEnforcement,
		int? maxLines = 1,
		int? minLines,
		bool expands = false,
		int? maxLength,
		ValueChanged<String>? onChanged,
		GestureTapCallback? onTap,
		bool onTapAlwaysCalled = false,
		TapRegionCallback? onTapOutside,
		VoidCallback? onEditingComplete,
		ValueChanged<String>? onFieldSubmitted,
		FormFieldSetter<String>? onSaved,
		FormFieldValidator<String>? validator,
		List<TextInputFormatter>? inputFormatters,
		bool? enabled,
		bool? ignorePointers,
		double cursorWidth = 2.0,
		double? cursorHeight,
		Radius? cursorRadius,
		Color? cursorColor,
		Color? cursorErrorColor,
		Brightness? keyboardAppearance,
		EdgeInsets scrollPadding = const EdgeInsets.all(20.0),
		bool? enableInteractiveSelection,
		TextSelectionControls? selectionControls,
		InputCounterWidgetBuilder? buildCounter,
		ScrollPhysics? scrollPhysics,
		Iterable<String>? autofillHints,
		AutovalidateMode? autovalidateMode,
		ScrollController? scrollController,
		String? restorationId,
		bool enableIMEPersonalizedLearning = true,
		MouseCursor? mouseCursor,
		EditableTextContextMenuBuilder? contextMenuBuilder = _defaultContextMenuBuilder,
		SpellCheckConfiguration? spellCheckConfiguration,
		TextMagnifierConfiguration? magnifierConfiguration,
		UndoHistoryController? undoController,
		AppPrivateCommandCallback? onAppPrivateCommand,
		bool? cursorOpacityAnimates,
		BoxHeightStyle selectionHeightStyle = ui.BoxHeightStyle.tight,
		BoxWidthStyle selectionWidthStyle = ui.BoxWidthStyle.tight,
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		ContentInsertionConfiguration? contentInsertionConfiguration,
		MaterialStatesController? statesController,
		Clip clipBehavior = Clip.hardEdge,
		bool scribbleEnabled = true,
		bool canRequestFocus = true
	})
	```
* 属性
	* onSaved → FormFieldSetter\<String>?：文本保存时的回调。
	* validator → FormFieldValidator\<String>?：文本内容校验器。

### Theme —— 主题

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > Theme
* 构造函数
	```dart
	Theme({
		Key? key,
		required ThemeData data,
		required Widget child
	})
	```
* 属性
	* child → Widget：子组件。
	* data → ThemeData：主题数据。
* 静态方法
	* of(BuildContext context) → ThemeData：获取上下文的主题数据。

### ThemeData —— 主题数据

* 构造函数
	```dart
	ThemeData({
		Iterable<Adaptation<Object>>? adaptations,
		bool? applyElevationOverlayColor,
		NoDefaultCupertinoThemeData? cupertinoOverrideTheme,
		Iterable<ThemeExtension>? extensions,
		InputDecorationTheme? inputDecorationTheme,
		MaterialTapTargetSize? materialTapTargetSize,
		PageTransitionsTheme? pageTransitionsTheme,
		TargetPlatform? platform,
		ScrollbarThemeData? scrollbarTheme,
		InteractiveInkFeatureFactory? splashFactory,
		bool? useMaterial3,
		VisualDensity? visualDensity,
		ColorScheme? colorScheme,
		Brightness? brightness,
		Color? colorSchemeSeed,
		Color? canvasColor,
		Color? cardColor,
		Color? dialogBackgroundColor,
		Color? disabledColor,
		Color? dividerColor,
		Color? focusColor,
		Color? highlightColor,
		Color? hintColor,
		Color? hoverColor,
		Color? indicatorColor,
		Color? primaryColor,
		Color? primaryColorDark,
		Color? primaryColorLight,
		MaterialColor? primarySwatch,
		Color? scaffoldBackgroundColor,
		Color? secondaryHeaderColor,
		Color? shadowColor,
		Color? splashColor,
		Color? unselectedWidgetColor,
		String? fontFamily,
		List<String>? fontFamilyFallback,
		String? package,
		IconThemeData? iconTheme,
		IconThemeData? primaryIconTheme,
		TextTheme? primaryTextTheme,
		TextTheme? textTheme,
		Typography? typography,
		ActionIconThemeData? actionIconTheme,
		AppBarTheme? appBarTheme,
		BadgeThemeData? badgeTheme,
		MaterialBannerThemeData? bannerTheme,
		BottomAppBarTheme? bottomAppBarTheme,
		BottomNavigationBarThemeData? bottomNavigationBarTheme,
		BottomSheetThemeData? bottomSheetTheme,
		ButtonThemeData? buttonTheme,
		CardTheme? cardTheme,
		CheckboxThemeData? checkboxTheme,
		ChipThemeData? chipTheme,
		DataTableThemeData? dataTableTheme,
		DatePickerThemeData? datePickerTheme,
		DialogTheme? dialogTheme,
		DividerThemeData? dividerTheme,
		DrawerThemeData? drawerTheme,
		DropdownMenuThemeData? dropdownMenuTheme,
		ElevatedButtonThemeData? elevatedButtonTheme,
		ExpansionTileThemeData? expansionTileTheme,
		FilledButtonThemeData? filledButtonTheme,
		FloatingActionButtonThemeData? floatingActionButtonTheme,
		IconButtonThemeData? iconButtonTheme,
		ListTileThemeData? listTileTheme,
		MenuBarThemeData? menuBarTheme,
		MenuButtonThemeData? menuButtonTheme,
		MenuThemeData? menuTheme,
		NavigationBarThemeData? navigationBarTheme,
		NavigationDrawerThemeData? navigationDrawerTheme,
		NavigationRailThemeData? navigationRailTheme,
		OutlinedButtonThemeData? outlinedButtonTheme,
		PopupMenuThemeData? popupMenuTheme,
		ProgressIndicatorThemeData? progressIndicatorTheme,
		RadioThemeData? radioTheme,
		SearchBarThemeData? searchBarTheme,
		SearchViewThemeData? searchViewTheme,
		SegmentedButtonThemeData? segmentedButtonTheme,
		SliderThemeData? sliderTheme,
		SnackBarThemeData? snackBarTheme,
		SwitchThemeData? switchTheme,
		TabBarTheme? tabBarTheme,
		TextButtonThemeData? textButtonTheme,
		TextSelectionThemeData? textSelectionTheme,
		TimePickerThemeData? timePickerTheme,
		ToggleButtonsThemeData? toggleButtonsTheme,
		TooltipThemeData? tooltipTheme,
		@Deprecated(
			'Use OverflowBar instead. '
			'This feature was deprecated after v3.21.0-10.0.pre.'
		)
		ButtonBarThemeData? buttonBarTheme
	})
	```
	
	* fontFamily：字体族。
* 属性
	* primaryColor → Color：主颜色。
* 方法
	* copyWith({Iterable\<Adaptation\<Object>>? adaptations, bool? applyElevationOverlayColor, NoDefaultCupertinoThemeData? cupertinoOverrideTheme, Iterable\<ThemeExtension>? extensions, InputDecorationTheme? inputDecorationTheme, MaterialTapTargetSize? materialTapTargetSize, PageTransitionsTheme? pageTransitionsTheme, TargetPlatform? platform, ScrollbarThemeData? scrollbarTheme, InteractiveInkFeatureFactory? splashFactory, VisualDensity? visualDensity, ColorScheme? colorScheme, Brightness? brightness, Color? canvasColor, Color? cardColor, Color? dialogBackgroundColor, Color? disabledColor, Color? dividerColor, Color? focusColor, Color? highlightColor, Color? hintColor, Color? hoverColor, Color? indicatorColor, Color? primaryColor, Color? primaryColorDark, Color? primaryColorLight, Color? scaffoldBackgroundColor, Color? secondaryHeaderColor, Color? shadowColor, Color? splashColor, Color? unselectedWidgetColor, IconThemeData? iconTheme, IconThemeData? primaryIconTheme, TextTheme? primaryTextTheme, TextTheme? textTheme, Typography? typography, ActionIconThemeData? actionIconTheme, AppBarTheme? appBarTheme, BadgeThemeData? badgeTheme, MaterialBannerThemeData? bannerTheme, BottomAppBarTheme? bottomAppBarTheme, BottomNavigationBarThemeData? bottomNavigationBarTheme, BottomSheetThemeData? bottomSheetTheme, ButtonThemeData? buttonTheme, CardTheme? cardTheme, CheckboxThemeData? checkboxTheme, ChipThemeData? chipTheme, DataTableThemeData? dataTableTheme, DatePickerThemeData? datePickerTheme, DialogTheme? dialogTheme, DividerThemeData? dividerTheme, DrawerThemeData? drawerTheme, DropdownMenuThemeData? dropdownMenuTheme, ElevatedButtonThemeData? elevatedButtonTheme, ExpansionTileThemeData? expansionTileTheme, FilledButtonThemeData? filledButtonTheme, FloatingActionButtonThemeData? floatingActionButtonTheme, IconButtonThemeData? iconButtonTheme, ListTileThemeData? listTileTheme, MenuBarThemeData? menuBarTheme, MenuButtonThemeData? menuButtonTheme, MenuThemeData? menuTheme, NavigationBarThemeData? navigationBarTheme, NavigationDrawerThemeData? navigationDrawerTheme, NavigationRailThemeData? navigationRailTheme, OutlinedButtonThemeData? outlinedButtonTheme, PopupMenuThemeData? popupMenuTheme, ProgressIndicatorThemeData? progressIndicatorTheme, RadioThemeData? radioTheme, SearchBarThemeData? searchBarTheme, SearchViewThemeData? searchViewTheme, SegmentedButtonThemeData? segmentedButtonTheme, SliderThemeData? sliderTheme, SnackBarThemeData? snackBarTheme, SwitchThemeData? switchTheme, TabBarTheme? tabBarTheme, TextButtonThemeData? textButtonTheme, TextSelectionThemeData? textSelectionTheme, TimePickerThemeData? timePickerTheme, ToggleButtonsThemeData? toggleButtonsTheme, TooltipThemeData? tooltipTheme, bool? useMaterial3, ButtonBarThemeData? buttonBarTheme}) → ThemeData：复制并自定义部分属性。

### TimeOfDay —— 一天中的时间

* 构造函数
	```dart
	TimeOfDay({required int hour, required int minute})
	```

	当前时间：
	
	```dart
	TimeOfDay.now()
	```

### Tooltip —— 工具提示

在长按时进行提示。

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > Tooltip
* 构造函数
	```dart
	Tooltip({
		Key? key,
		String? message,
		InlineSpan? richMessage,
		double? height,
		EdgeInsetsGeometry? padding,
		EdgeInsetsGeometry? margin,
		double? verticalOffset,
		bool? preferBelow,
		bool? excludeFromSemantics,
		Decoration? decoration,
		TextStyle? textStyle,
		TextAlign? textAlign,
		Duration? waitDuration,
		Duration? showDuration,
		Duration? exitDuration,
		bool enableTapToDismiss = true,
		TooltipTriggerMode? triggerMode,
		bool? enableFeedback,
		TooltipTriggeredCallback? onTriggered,
		Widget? child
	})
	```
* 属性
	* child → Widget?

### UnderlineTabIndicator —— 下划线

为子组件添加下划线。

* 继承
	Object > Decoration > UnderlineTabIndicator
* 构造函数
	```dart
	UnderlineTabIndicator({
		BorderRadius? borderRadius,
		BorderSide borderSide = const BorderSide(width: 2.0, color: Colors.white),
		EdgeInsetsGeometry insets = EdgeInsets.zero
	})
	```

### UserAccountsDrawerHeader —— 展示用户账号信息的抽屉头

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > UserAccountsDrawerHeader
* 构造函数
	```dart
	UserAccountsDrawerHeader({
		Key? key,
		Decoration? decoration,
		EdgeInsetsGeometry? margin = const EdgeInsets.only(bottom: 8.0),
		Widget? currentAccountPicture,
		List<Widget>? otherAccountsPictures,
		Size currentAccountPictureSize = const Size.square(72.0),
		Size otherAccountsPicturesSize = const Size.square(40.0),
		required Widget? accountName,
		required Widget? accountEmail,
		VoidCallback? onDetailsPressed,
		Color arrowColor = Colors.white
	})
	```

## 枚举

### TabBarIndicatorSize —— 选项卡栏指示器大小

* 值
	* label → const TabBarIndicatorSize：每个选项卡调整为适合实际内容的宽度。
	* tab → const TabBarIndicatorSize：所有选项卡等宽，均分空间。

## 函数

### showDatePicker —— 展示日期选择器

调用系统的组件。

```dart
Future<DateTime?> showDatePicker({
	required BuildContext context,
	DateTime? initialDate,
	required DateTime firstDate,
	required DateTime lastDate,
	DateTime? currentDate,
	DatePickerEntryMode initialEntryMode = DatePickerEntryMode.calendar,
	SelectableDayPredicate? selectableDayPredicate,
	String? helpText,
	String? cancelText,
	String? confirmText,
	Locale? locale,
	bool barrierDismissible = true,
	Color? barrierColor,
	String? barrierLabel,
	bool useRootNavigator = true,
	RouteSettings? routeSettings,
	TextDirection? textDirection,
	TransitionBuilder? builder,
	DatePickerMode initialDatePickerMode = DatePickerMode.day,
	String? errorFormatText,
	String? errorInvalidText,
	String? fieldHintText,
	String? fieldLabelText,
	TextInputType? keyboardType,
	Offset? anchorPoint,
	ValueChanged<DatePickerEntryMode>? onDatePickerModeChange,
	Icon? switchToInputEntryModeIcon,
	Icon? switchToCalendarEntryModeIcon,
})
```

* context：构建上下文。
* firstDate：可选择的开始日期。
* initialDate：初始显示的日期。
* lastDate：可选中的结束日期。

### showDialog —— 展示对话框

```dart
Future<T?> showDialog<T>({
	required BuildContext context,
	required WidgetBuilder builder,
	bool barrierDismissible = true,
	Color? barrierColor,
	String? barrierLabel,
	bool useSafeArea = true,
	bool useRootNavigator = true,
	RouteSettings? routeSettings,
	Offset? anchorPoint,
	TraversalEdgeBehavior? traversalEdgeBehavior,
})
```

### showTimePicker —— 展示时间选择器

调用系统的组件。

```dart
Future<TimeOfDay?> showTimePicker({
	required BuildContext context,
	required TimeOfDay initialTime,
	TransitionBuilder? builder,
	bool barrierDismissible = true,
	Color? barrierColor,
	String? barrierLabel,
	bool useRootNavigator = true,
	TimePickerEntryMode initialEntryMode = TimePickerEntryMode.dial,
	String? cancelText,
	String? confirmText,
	String? helpText,
	String? errorInvalidText,
	String? hourLabelText,
	String? minuteLabelText,
	RouteSettings? routeSettings,
	EntryModeChangeCallback? onEntryModeChanged,
	Offset? anchorPoint,
	Orientation? orientation,
})
```

* context：构建上下文。
* initialTime：初始显示的时间。

# painting 【绘图库】

```dart
import 'package:flutter/painting.dart';
```

## 类

### Alignment —— 对齐

* 继承
	Object > AlignmentGeometry > Alignment
* 构造函数
	```dart
	Alignment(double x, double y)
	```
* 常量
	* bottomCenter → const Alignment
	* bottomLeft → const Alignment
	* bottomRight → const Alignment
	* center → const Alignment
	* centerLeft → const Alignment
	* centerRight → const Alignment
	* topCenter → const Alignment
	* topLeft → const Alignment
	* topRight → const Alignment

### AlignmentDirectional —— 对齐方向

* 继承
	Object > AlignmentGeometry > AlignmentDirectional
* 构造函数
	```dart
	AlignmentDirectional(double start, double y)
	```
* 常量
	* bottomCenter → const AlignmentDirectional
	* bottomEnd → const AlignmentDirectional
	* bottomStart → const AlignmentDirectional
	* center → const AlignmentDirectional
	* centerEnd → const AlignmentDirectional
	* centerStart → const AlignmentDirectional
	* topCenter → const AlignmentDirectional
	* topEnd → const AlignmentDirectional
	* topStart → const AlignmentDirectional

### AssetImage —— 资源图片

* 继承
	Object > ImageProvider\<AssetBundleImageKey> > AssetBundleImageProvider > AssetImage
* 构造函数
	```dart
	AssetImage(
		String assetName,
		{
		AssetBundle? bundle,
		String? package
		}
	)
	```

### Axis —— 坐标轴

* 继承
	Object > Enum > Axis
* 值
	* horizontal → const Axis：水平坐标轴。
	* vertical → const Axis：垂直坐标轴。

### BorderRadius —— 边框圆角半径

* 继承
	Object > BorderRadiusGeometry > BorderRadius
* 构造函数
	```dart
	BorderRadius.circular(double radius)
	```

### BoxDecoration —— 盒装饰

* 继承
	Object > Decoration > BoxDecoration
* 构造函数
	```dart
	BoxDecoration({
		Color? color,
		DecorationImage? image,
		BoxBorder? border,
		BorderRadiusGeometry? borderRadius,
		List<BoxShadow>? boxShadow,
		Gradient? gradient,
		BlendMode? backgroundBlendMode,
		BoxShape shape = BoxShape.rectangle
	})
	```
* 属性
	* backgroundBlendMode → BlendMode?：背景混合模式。
	* border → BoxBorder?：边框。
	* borderRadius → BorderRadiusGeometry?：边框圆角半径。
	* boxShadow → List\<BoxShadow>?：阴影。
	* color → Color?：颜色。
	* gradient → Gradient?：渐变。
	* image → DecorationImage?：图片。
	* shape → BoxShape：形状。

### EdgeInsets —— 边缘镶嵌

* 继承
	Object > EdgeInsetsGeometry > EdgeInsets
* 构造函数
	
	所有边距使用相同的值：

	```dart
	EdgeInsets.all(double value)
	```
	
	分布指定四个方向的边距：
	
	```dart
	EdgeInsets.fromLTRB(double left, double top, double right, double bottom)
	```
	
	可只指定某些方向的边距：
	
	```dart
	EdgeInsets.only({double left = 0.0, double top = 0.0, double right = 0.0, double bottom = 0.0})
	```
	
	可只指定某些对称方向的边距：
	
	```dart
	EdgeInsets.symmetric({double vertical = 0.0, double horizontal = 0.0})
	```
* 常量
	* zero → const EdgeInsets

### FlutterLogoDecoration —— Flutter的logo装饰

* 继承
	Object > Decoration > FlutterLogoDecoration
* 构造函数
	```dart
	FlutterLogoDecoration({
		Color textColor = const Color(0xFF757575),
		FlutterLogoStyle style = FlutterLogoStyle.markOnly,
		EdgeInsets margin = EdgeInsets.zero
	})
	```

### LinearGradient —— 线性渐变

* 继承
	Object > Gradient > LinearGradient
* 构造函数
	```dart
	LinearGradient({
		AlignmentGeometry begin = Alignment.centerLeft,
		AlignmentGeometry end = Alignment.centerRight,
		required List<Color> colors,
		List<double>? stops,
		TileMode tileMode = TileMode.clamp,
		GradientTransform? transform
	})
	```

### NetworkImage —— 网络图片

* 继承
	Object > ImageProvider\<NetworkImage> > NetworkImage
* 构造函数
	```dart
	NetworkImage(
		String url,
		{
		double scale,
		Map<String, String>? headers
		}
	)
	```

### ShapeDecoration —— 形状装饰

* 继承
	Object > Decoration > ShapeDecoration
* 构造函数
	```dart
	ShapeDecoration({
		Color? color,
		DecorationImage? image,
		Gradient? gradient,
		List<BoxShadow>? shadows,
		required ShapeBorder shape
	})
	```

### TextSpan —— 文本片段

* 继承
	Object > DiagnosticableTree > InlineSpan > TextSpan
* 构造函数
	```dart
	TextSpan({
		String? text,
		List<InlineSpan>? children,
		TextStyle? style,
		GestureRecognizer? recognizer,
		MouseCursor? mouseCursor,
		PointerEnterEventListener? onEnter,
		PointerExitEventListener? onExit,
		String? semanticsLabel,
		Locale? locale,
		bool? spellOut
	})
	```
* 属性
	* recognizer → GestureRecognizer?：触摸事件手势识别器。

### TextStyle —— 文本样式

* 构造函数
	```dart
	TextStyle({
		bool inherit = true,
		Color? color,
		Color? backgroundColor,
		double? fontSize,
		FontWeight? fontWeight,
		FontStyle? fontStyle,
		double? letterSpacing,
		double? wordSpacing,
		TextBaseline? textBaseline,
		double? height,
		TextLeadingDistribution? leadingDistribution,
		Locale? locale,
		Paint? foreground,
		Paint? background,
		List<Shadow>? shadows,
		List<FontFeature>? fontFeatures,
		List<FontVariation>? fontVariations,
		TextDecoration? decoration,
		Color? decorationColor,
		TextDecorationStyle? decorationStyle,
		double? decorationThickness,
		String? debugLabel,
		String? fontFamily,
		List<String>? fontFamilyFallback,
		String? package,
		TextOverflow? overflow
	})
	```

	* package：包名。字体在某个包中但该字体未在pubspec.yaml中声明时使用。
* 属性
	* background → Paint?：背景色。
	* color → Color?：文本颜色。
	* fontFamily → String?：字体族。
	* fontSize → double?：字体大小。
	* decoration → TextDecoration?：背景装饰。

## 枚举

### ImageRepeat —— 图片重复方式

* 值
	* repeat → const ImageRepeat
	* repeatX → const ImageRepeat
	* repeatY → const ImageRepeat
	* noRepeat → const ImageRepeat

### TextOverflow —— 文本溢出方式

* 值
	* clip → const TextOverflow
	* fade → const TextOverflow
	* ellipsis → const TextOverflow：溢出的文本以“...”方式表示。
	* visible → const TextOverflow

### VerticalDirection —— 垂直方向

* 值
	* up → const VerticalDirection：从下到上。
	* down → const VerticalDirection：从上到下。

# path 【跨平台路径库】

```dart
import 'package:path/path.dart';
```

## 函数

* join(String part1, [String? part2, String? part3, String? part4, String? part5, String? part6, String? part7, String? part8, String? part9, String? part10, String? part11, String? part12, String? part13, String? part14, String? part15, String? part16]) → String：路径拼接。

# rendering 【渲染库】

```dart
import 'package:flutter/rendering.dart';
```

## 类

### BoxConstraints —— 盒约束

* 继承
	Object > Constraints > BoxConstraints
* 构造函数
	```dart
	BoxConstraints({
		double minWidth = 0.0,
		double maxWidth = double.infinity,
		double minHeight = 0.0,
		double maxHeight = double.infinity
	})
	```
* 属性
	* maxHeight → double：最大高度。
	* maxWidth → double：最大宽度。
	* minHeight → double：最小高度。
	* minWidth → double：最小宽度。

### SliverGridDelegateWithFixedCrossAxisCount —— Sliver家族的固定交叉轴方向元素数量的网格代理

滚动方向为主轴方向。

* 继承
	Object > SliverGridDelegate > SliverGridDelegateWithFixedCrossAxisCount
* 构造函数
	```dart
	SliverGridDelegateWithFixedCrossAxisCount({
		required int crossAxisCount,
		double mainAxisSpacing = 0.0,
		double crossAxisSpacing = 0.0,
		double childAspectRatio = 1.0,
		double? mainAxisExtent
	})
	```
* 属性
	* childAspectRatio → double：子组件在交叉轴方向和主轴方向上的尺寸比例。
	* crossAxisCount → int：交叉轴方向元素数量。
	* crossAxisSpacing → double：交叉轴方向子组件间距。
	* mainAxisExtent → double?：主轴方向子组件间距。

### SliverGridDelegateWithMaxCrossAxisExtent —— Sliver家族的指定交叉轴方向元素最大尺寸的网格代理

滚动方向为主轴方向。

* 继承
	Object > SliverGridDelegate > SliverGridDelegateWithMaxCrossAxisExtent
* 构造函数
	```dart
	SliverGridDelegateWithMaxCrossAxisExtent({
		required double maxCrossAxisExtent,
		double mainAxisSpacing = 0.0,
		double crossAxisSpacing = 0.0,
		double childAspectRatio = 1.0,
		double? mainAxisExtent
	})
	```
* 属性
	* maxCrossAxisExtent → double：元素在交叉轴方向上的最大尺寸。因为所有子组件是平均分配交叉轴的空间，实际值会在此值的一个范围附近。

## 枚举

### CrossAxisAlignment —— 交叉轴对齐方式

* 值
	* start → const CrossAxisAlignment
	* end → const CrossAxisAlignment
	* center → const CrossAxisAlignment
	* stretch → const CrossAxisAlignment
	* baseline → const CrossAxisAlignment

### DecorationPosition —— 装饰位置

* 值
	* background → const DecorationPosition：子组件后。
	* foreground → const DecorationPosition：子组件前。

### HitTestBehavior —— 点击行为

* 值
	* deferToChild → const HitTestBehavior：事件会逐个在每一层组件上响应。
	* opaque → const HitTestBehavior：强制视作不透明组件，使组件全部空间都可响应事件，阻断其背后组件的响应。（默认情况下，组件的透明部分会被穿透，不响应事件。）
	* translucent → const HitTestBehavior：强制视作半透明组件，会被穿透，背后组件亦可响应事件。

### MainAxisAlignment —— 主轴对齐方式

* 值
	* start → const MainAxisAlignment
	* end → const MainAxisAlignment
	* center → const MainAxisAlignment
	* spaceBetween → const MainAxisAlignment
	* spaceAround → const MainAxisAlignment
	* spaceEvenly → const MainAxisAlignment

### MainAxisSize —— 主轴所占空间大小

* 值
	* min → const MainAxisSize：尽可能少占用空间，即可以容纳所有子组件的最小尺寸。
	* max → const MainAxisSize：尽可能多占用空间，即使子组件没有占满。

### StackFit —— 栈布局/堆叠布局的适应方式

* 值
	* loose → const StackFit：使用子组件的大小。
	* expand → const StackFit：填充整个`Stack`。
	* passthrough → const StackFit

## 属性

### debugPaintBaselinesEnabled —— 是否显示绘图的基线

对于具有基线的组件，文字基线以亮绿色显示，表意基线以橙色显示。

```dart
bool debugPaintBaselinesEnabled
```

### debugPaintPointersEnabled —— 是否显示绘图的点击位

被点击的组件以深青色高亮显示。

```dart
bool debugPaintPointersEnabled
```

### debugPaintSizeEnabled —— 是否显示绘图的大小

边界以青色边框显示，对齐方式以黄色箭头显示。

```dart
bool debugPaintSizeEnabled
```

## 函数

### debugDumpLayerTree —— 转储层级关系

```dart
void debugDumpLayerTree()
```

### debugDumpRenderTree —— 转储渲染层

```dart
void debugDumpRenderTree()
```

### debugDumpSemanticsTree —— 转储语义调试

```dart
void debugDumpSemanticsTree([ DebugSemanticsDumpOrder childOrder = DebugSemanticsDumpOrder.traversalOrder ])
```

# scheduler 【调度库】

```dart
import 'package:flutter/scheduler.dart';
```

## 属性

### debugPrintBeginFrameBanner —— 打印帧开始

debugPrintBeginFrameBanner ↔ bool

### debugPrintEndFrameBanner —— 打印帧结束

debugPrintEndFrameBanner ↔ bool

### timeDilation —— 动画时间放慢倍数

应只在程序启动后设置一次，否则可能引发一些副作用。

# services 【平台服务库】

```dart
import 'package:flutter/services.dart';
```

## 类

### AssetBundle —— 资源包

* 方法
	* loadString(String key, {bool cache = true}) → Future\<String>：加载字符串内容。

### MethodChannel —— 平台通道

Flutter侧称为客户端（Client），原生侧称为宿主（Host）。消息的传递是异步的，保证前台UI不会因此而停止响应。发送和接收消息时，序列化和反序列化会自动进行。

* 构造函数
	```dart
	MethodChannel(
		String name,
		[
		MethodCodec codec = const StandardMethodCodec(),
		BinaryMessenger? binaryMessenger
		]
	)
	```
* 属性
	* name → String：通道名称。在单个应用中所有通道名称必需唯一，建议通道名称使用有规律的前缀。
* 方法
	* invokeMethod\<T>(String method, [dynamic arguments]) → Future\<T?>：调用原生侧的方法。

### TextInputType —— 文本输入类型

* 常量
	* datetime → const TextInputType：日期时间。
	* emailAddress → const TextInputType：邮箱。
	* number → const TextInputType：数字。
	* phone → const TextInputType：电话号码。
	* text → const TextInputType：文本。
	* url → const TextInputType：网址。

## 枚举

### TextInputAction —— 文本输入操作

* 值
	* none → const TextInputAction
	* unspecified → const TextInputAction
	* done → const TextInputAction
	* go → const TextInputAction
	* search → const TextInputAction
	* send → const TextInputAction
	* next → const TextInputAction
	* previous → const TextInputAction
	* continueAction → const TextInputAction
	* join → const TextInputAction
	* route → const TextInputAction
	* emergencyCall → const TextInputAction
	* newline → const TextInputAction

# widgets 【组件库】

```dart
import 'package:flutter/widgets.dart';
```

## 函数

### runApp —— 运行应用程序

```dart
runApp(Widget app) → void
```

## 类

### AbsorbPointer —— 吞噬指向事件

接收并丢弃事件，子组件无法接收。

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > SingleChildRenderObjectWidget > AbsorbPointer
* 构造函数
	```dart
	AbsorbPointer({
		Key? key,
		bool absorbing = true,
		@Deprecated(
			'Use ExcludeSemantics or create a custom absorb pointer widget instead. '
			'This feature was deprecated after v3.8.0-12.0.pre.'
		)
		bool? ignoringSemantics, Widget? child
	})
	```

### BottomNavigationBarItem —— 底部导航栏项

* 构造函数
	```dart
	BottomNavigationBarItem({
		Key? key,
		required Widget icon,
		String? label,
		Widget? activeIcon,
		Color? backgroundColor,
		String? tooltip
	})
	```
* 属性
	* icon → Widget
	* label → String?

### BouncingScrollPhysics —— 气球跳跃式滚动物理效果

* 继承
	Object > ScrollPhysics > BouncingScrollPhysics
* 构造函数
	```dart
	BouncingScrollPhysics({
		ScrollDecelerationRate decelerationRate = ScrollDecelerationRate.normal,
		ScrollPhysics? parent
	})
	```

### BuildContext —— 构建上下文

* 属性
	* widget → Widget：对应的组件。

### Builder —— 无状态组件构建器

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > Builder
* 构造函数
	```dart
	Builder({
		Key? key,
		required WidgetBuilder builder
	})
	```

### Center —— 居中

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > SingleChildRenderObjectWidget > Align > Center
* 构造函数
	```dart
	Center({
		Key? key,
		double? widthFactor,
		double? heightFactor,
		Widget? child
	})
	```

### ClampingScrollPhysics —— 夹紧滚动物理效果

* 继承
	Object > ScrollPhysics > ClampingScrollPhysics
* 构造函数
	```dart
	ClampingScrollPhysics({ScrollPhysics? parent})
	```

### Column —— 列布局/垂直布局

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > MultiChildRenderObjectWidget > Flex > Column
* 构造函数
	```dart
	Column({
		Key? key,
		MainAxisAlignment mainAxisAlignment = MainAxisAlignment.start,
		MainAxisSize mainAxisSize = MainAxisSize.max,
		CrossAxisAlignment crossAxisAlignment = CrossAxisAlignment.center,
		TextDirection? textDirection,
		VerticalDirection verticalDirection = VerticalDirection.down,
		TextBaseline? textBaseline,
		List<Widget> children = const <Widget>[]
	})
	```
* 属性
	* children → List\<Widget>：子组件列表。
	* crossAxisAlignment → CrossAxisAlignment：交叉轴对齐方式。
	* mainAxisAlignment → MainAxisAlignment：主轴对齐方式。当`mainAxisSize`为`MainAxisSize.min`时，本属性不起作用，因为空间已被占满。
	* mainAxisSize → MainAxisSize：主轴所占空间大小。默认为MainAxisSize.max。
	* textDirection → TextDirection?：子组件的水平排布方向。默认跟随系统，大多为`TextDirection.ltr`。
	* verticalDirection → VerticalDirection：子组件的垂直排布方向。默认为`VerticalDirection.down`。

### ConstrainedBox —— 约束盒

当子组件超出最大约束时会显示异常，当子组件未填满最小约束时未填充部分显示背景色。

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > SingleChildRenderObjectWidget > ConstrainedBox
* 构造函数
	```dart
	ConstrainedBox({
		Key? key,
		required BoxConstraints constraints,
		Widget? child
	})
	```
* 属性
	* child → Widget?：子组件。
	* constraints → BoxConstraints：盒约束条件。

### Container —— 容器

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > Container
* 构造函数
	```dart
	Container({
		Key? key,
		AlignmentGeometry? alignment,
		EdgeInsetsGeometry? padding,
		Color? color,
		Decoration? decoration,
		Decoration? foregroundDecoration,
		double? width,
		double? height,
		BoxConstraints? constraints,
		EdgeInsetsGeometry? margin,
		Matrix4? transform,
		AlignmentGeometry? transformAlignment,
		Widget? child,
		Clip clipBehavior = Clip.none
	})
	```
* 属性：
	* alignment → AlignmentGeometry?
	* child → Widget?
	* color → Color?
	* constraints → BoxConstraints?
	* decoration → Decoration?
	* foregroundDecoration → Decoration?
	* margin → EdgeInsetsGeometry?：外边距。
	* padding → EdgeInsetsGeometry?：内边距。
	* transform → Matrix4?

### CustomScrollView —— 自定义滚动视图

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > ScrollView > CustomScrollView
* 构造函数
	```dart
	CustomScrollView({
		Key? key,
		Axis scrollDirection = Axis.vertical,
		bool reverse = false,
		ScrollController? controller,
		bool? primary,
		ScrollPhysics? physics,
		ScrollBehavior? scrollBehavior,
		bool shrinkWrap = false,
		Key? center, double anchor = 0.0,
		double? cacheExtent,
		List<Widget> slivers = const <Widget>[],
		int? semanticChildCount,
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		ScrollViewKeyboardDismissBehavior keyboardDismissBehavior = ScrollViewKeyboardDismissBehavior.manual,
		String? restorationId,
		Clip clipBehavior = Clip.hardEdge,
		HitTestBehavior hitTestBehavior = HitTestBehavior.opaque
	})
	```
* 属性
	* slivers → List\<Widget>：子组件列表。必须属于Sliver家族。

### DecoratedBox —— 装饰盒

为子组件添加视觉效果。

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > SingleChildRenderObjectWidget > DecoratedBox
* 构造函数
	```dart
	DecoratedBox({
		Key? key,
		required Decoration decoration,
		DecorationPosition position = DecorationPosition.background,
		Widget? child
	})
	```
* 属性
	* child → Widget?：子组件。
	* decoration → Decoration：装饰。
	* position → DecorationPosition：装饰相对于子组件的位置。

### DefaultAssetBundle —— 默认资源包

* 继承
	Object > DiagnosticableTree > Widget > ProxyWidget > InheritedWidget > DefaultAssetBundle
* 静态方法
	* of(BuildContext context) → AssetBundle：返回构建上下文的资源包。

### DefaultTextStyle —— 默认文本样式

其子组件的文本以此为默认文本样式。

* 继承
	Object > DiagnosticableTree > Widget > ProxyWidget > InheritedWidget > InheritedTheme > DefaultTextStyle
* 构造函数
	```dart
	DefaultTextStyle({
		Key? key,
		required TextStyle style,
		TextAlign? textAlign,
		bool softWrap = true,
		TextOverflow overflow = TextOverflow.clip,
		int? maxLines,
		TextWidthBasis textWidthBasis = TextWidthBasis.parent,
		TextHeightBehavior? textHeightBehavior,
		required Widget child
	})
	```

### Element —— 元素

可见的。

* 继承
	Object > DiagnosticableTree > Element
* 构造函数
	```dart
	Element(Widget widget)
	```

### Expanded —— 膨胀

令子组件尽可能地填充可用空间。

* 继承
	Object > DiagnosticableTree > Widget > ProxyWidget > ParentDataWidget\<FlexParentData> > Flexible > Expanded
* 构造函数
	```dart
	Expanded({
		Key? key,
		int flex = 1,
		required Widget child
	})
	```
* 属性
	* flex → int：弹性系数。子组件占父组件的比例为：`flex`/父组件所有子组件的`flex`之和。

### FadeInImage —— 淡入图片

初始显示占位图片，实际图片加载完成后淡入替换。

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > FadeInImage
* 构造函数
	```dart
	FadeInImage.assetNetwork({
		Key? key,
		required String placeholder,
		ImageErrorWidgetBuilder? placeholderErrorBuilder,
		required String image,
		ImageErrorWidgetBuilder? imageErrorBuilder,
		AssetBundle? bundle,
		double? placeholderScale,
		double imageScale = 1.0,
		bool excludeFromSemantics = false,
		String? imageSemanticLabel,
		Duration fadeOutDuration = const Duration(milliseconds: 300),
		Curve fadeOutCurve = Curves.easeOut,
		Duration fadeInDuration = const Duration(milliseconds: 700),
		Curve fadeInCurve = Curves.easeIn,
		double? width,
		double? height,
		BoxFit? fit,
		Color? color,
		BlendMode? colorBlendMode,
		Color? placeholderColor,
		BlendMode? placeholderColorBlendMode,
		BoxFit? placeholderFit,
		FilterQuality filterQuality = FilterQuality.medium,
		FilterQuality? placeholderFilterQuality,
		AlignmentGeometry alignment = Alignment.center,
		ImageRepeat repeat = ImageRepeat.noRepeat,
		bool matchTextDirection = false,
		int? placeholderCacheWidth,
		int? placeholderCacheHeight,
		int? imageCacheWidth,
		int? imageCacheHeight
	})
	```

### FocusNode —— 焦点

* 构造函数
	```dart
	FocusNode({
		String? debugLabel,
		@Deprecated(
			'Use onKeyEvent instead. '
			'This feature was deprecated after v3.18.0-2.0.pre.'
		)
		FocusOnKeyCallback? onKey,
		FocusOnKeyEventCallback? onKeyEvent,
		bool skipTraversal = false,
		bool canRequestFocus = true,
		bool descendantsAreFocusable = true,
		bool descendantsAreTraversable = true
	})
	```

### Form —— 表单

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > Form
* 构造函数
	```dart
	Form({
		Key? key,
		required Widget child,
		bool? canPop,
		@Deprecated(
			'Use onPopInvokedWithResult instead. '
			'This feature was deprecated after v3.22.0-12.0.pre.'
		)
		PopInvokedCallback? onPopInvoked,
		PopInvokedWithResultCallback<Object?>? onPopInvokedWithResult,
		@Deprecated(
			'Use canPop and/or onPopInvokedWithResult instead. '
			'This feature was deprecated after v3.12.0-1.0.pre.'
		)
		WillPopCallback? onWillPop,
		VoidCallback? onChanged,
		AutovalidateMode? autovalidateMode
	})
	```

### FormState —— 表单状态

* 继承
	Object > State\<Form> > FormState
* 构造函数
	```dart
	FormState()
	```
* 方法
	* reset() → void：表单重置。
	* save() → void：表单保存。
	* validate() → bool：表单校验。

### FutureBuilder\<T> —— Future构建器

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > FutureBuilder
* 构造函数
	```dart
	FutureBuilder({
		Key? key,
		required Future<T>? future,
		T? initialData,
		required AsyncWidgetBuilder<T> builder
	})
	```

### Flex —— 弹性布局

按比例摆放子组件。要求子组件可以被`Expanded`包裹。

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > MultiChildRenderObjectWidget > Flex
* 构造函数
	```dart
	Flex({
		Key? key,
		required Axis direction,
		MainAxisAlignment mainAxisAlignment = MainAxisAlignment.start,
		MainAxisSize mainAxisSize = MainAxisSize.max,
		CrossAxisAlignment crossAxisAlignment = CrossAxisAlignment.center,
		TextDirection? textDirection,
		VerticalDirection verticalDirection = VerticalDirection.down,
		TextBaseline? textBaseline,
		Clip clipBehavior = Clip.none,
		List<Widget> children = const <Widget>[]
	})
	```
* 属性
	* direction → Axis：子组件的排布方向。

### FormFieldState\<T> —— 表单字段状态

* 继承
	Object > State\<FormField\<T>> > FormFieldState
* 方法
	* reset() → void：重置表单字段至其初始值。
	* save() → void：保存。会调用`FormField<T>`的`onSaved`方法。
	* validate() → bool：校验表单内容。会调用`FormField<T>`的`validator`方法。

### GestureDetector —— 手势识别

内部使用`GestureRecognizer`实现。

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > GestureDetector
* 构造函数
	```dart
	GestureDetector({
		Key? key,
		Widget? child,
		GestureTapDownCallback? onTapDown,
		GestureTapUpCallback? onTapUp,
		GestureTapCallback? onTap,
		GestureTapCancelCallback? onTapCancel,
		GestureTapCallback? onSecondaryTap,
		GestureTapDownCallback? onSecondaryTapDown,
		GestureTapUpCallback? onSecondaryTapUp,
		GestureTapCancelCallback? onSecondaryTapCancel,
		GestureTapDownCallback? onTertiaryTapDown,
		GestureTapUpCallback? onTertiaryTapUp,
		GestureTapCancelCallback? onTertiaryTapCancel,
		GestureTapDownCallback? onDoubleTapDown,
		GestureTapCallback? onDoubleTap,
		GestureTapCancelCallback? onDoubleTapCancel,
		GestureLongPressDownCallback? onLongPressDown,
		GestureLongPressCancelCallback? onLongPressCancel,
		GestureLongPressCallback? onLongPress,
		GestureLongPressStartCallback? onLongPressStart,
		GestureLongPressMoveUpdateCallback? onLongPressMoveUpdate,
		GestureLongPressUpCallback? onLongPressUp,
		GestureLongPressEndCallback? onLongPressEnd,
		GestureLongPressDownCallback? onSecondaryLongPressDown,
		GestureLongPressCancelCallback? onSecondaryLongPressCancel,
		GestureLongPressCallback? onSecondaryLongPress,
		GestureLongPressStartCallback? onSecondaryLongPressStart,
		GestureLongPressMoveUpdateCallback? onSecondaryLongPressMoveUpdate,
		GestureLongPressUpCallback? onSecondaryLongPressUp,
		GestureLongPressEndCallback? onSecondaryLongPressEnd,
		GestureLongPressDownCallback? onTertiaryLongPressDown,
		GestureLongPressCancelCallback? onTertiaryLongPressCancel,
		GestureLongPressCallback? onTertiaryLongPress,
		GestureLongPressStartCallback? onTertiaryLongPressStart,
		GestureLongPressMoveUpdateCallback? onTertiaryLongPressMoveUpdate,
		GestureLongPressUpCallback? onTertiaryLongPressUp,
		GestureLongPressEndCallback? onTertiaryLongPressEnd,
		GestureDragDownCallback? onVerticalDragDown,
		GestureDragStartCallback? onVerticalDragStart,
		GestureDragUpdateCallback? onVerticalDragUpdate,
		GestureDragEndCallback? onVerticalDragEnd,
		GestureDragCancelCallback? onVerticalDragCancel,
		GestureDragDownCallback? onHorizontalDragDown,
		GestureDragStartCallback? onHorizontalDragStart,
		GestureDragUpdateCallback? onHorizontalDragUpdate,
		GestureDragEndCallback? onHorizontalDragEnd,
		GestureDragCancelCallback? onHorizontalDragCancel,
		GestureForcePressStartCallback? onForcePressStart,
		GestureForcePressPeakCallback? onForcePressPeak,
		GestureForcePressUpdateCallback? onForcePressUpdate,
		GestureForcePressEndCallback? onForcePressEnd,
		GestureDragDownCallback? onPanDown,
		GestureDragStartCallback? onPanStart,
		GestureDragUpdateCallback? onPanUpdate,
		GestureDragEndCallback? onPanEnd,
		GestureDragCancelCallback? onPanCancel,
		GestureScaleStartCallback? onScaleStart,
		GestureScaleUpdateCallback? onScaleUpdate,
		GestureScaleEndCallback? onScaleEnd,
		HitTestBehavior? behavior,
		bool excludeFromSemantics = false,
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		bool trackpadScrollCausesScale = false,
		Offset trackpadScrollToScaleFactor = kDefaultTrackpadScrollToScaleFactor,
		Set<PointerDeviceKind>? supportedDevices
	})
	```
* 属性
	* onDoubleTap → GestureTapCallback?：双击。
	* onHorizontalDragDown → GestureDragDownCallback?：水平方向的拖拽按下。
	* onHorizontalDragEnd → GestureDragEndCallback?：水平方向的拖拽结束。
	* onHorizontalDragUpdate → GestureDragUpdateCallback?：水平方向的拖拽中。
	* onLongPress → GestureLongPressCallback?：长按。
	* onPanDown → GestureDragDownCallback?：滑动按下。`onPanDown`与`onTap`共存时可能只有`onPanDown`事件响应。
	* onPanEnd → GestureDragEndCallback?：滑动结束。
	* onPanUpdate → GestureDragUpdateCallback?：滑动中。
	* onScaleEnd → GestureScaleEndCallback?：缩放结束。
	* onScaleStart → GestureScaleStartCallback?：缩放开始。
	* onScaleUpdate → GestureScaleUpdateCallback?：缩放中。
	* onTap → GestureTapCallback?：单击。`onPanDown`与`onTap`共存时可能只有`onPanDown`事件响应。
	* onVerticalDragDown → GestureDragDownCallback?：垂直方向的拖拽按下。
	* onVerticalDragEnd → GestureDragEndCallback?：垂直方向的拖拽结束。
	* onVerticalDragUpdate → GestureDragUpdateCallback?：垂直方向的拖拽

### GlobalKey\<T extends State\<StatefulWidget>> —— 全局键

* 构造函数
	```dart
	GlobalKey({String? debugLabel})
	```
* 属性
	* currentState → T?：引用此键的对象的当前状态。

### GridView —— 滚动网格视图

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > ScrollView > BoxScrollView > GridView
* 构造函数

	在有足够简单的子组件的情况下使用：

	```dart
	GridView({
		Key? key,
		Axis scrollDirection = Axis.vertical,
		bool reverse = false,
		ScrollController? controller,
		bool? primary,
		ScrollPhysics? physics,
		bool shrinkWrap = false,
		EdgeInsetsGeometry? padding,
		required SliverGridDelegate gridDelegate,
		bool addAutomaticKeepAlives = true,
		bool addRepaintBoundaries = true,
		bool addSemanticIndexes = true,
		double? cacheExtent,
		List<Widget> children = const <Widget>[],
		int? semanticChildCount,
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		Clip clipBehavior = Clip.hardEdge,
		ScrollViewKeyboardDismissBehavior keyboardDismissBehavior = ScrollViewKeyboardDismissBehavior.manual,
		String? restorationId,
		HitTestBehavior hitTestBehavior = HitTestBehavior.opaque
	})
	```

	适合在子组件较多的情况下使用：

	```dart
	GridView.builder({
		Key? key,
		Axis scrollDirection = Axis.vertical,
		bool reverse = false,
		ScrollController? controller,
		bool? primary,
		ScrollPhysics? physics,
		bool shrinkWrap = false,
		EdgeInsetsGeometry? padding,
		required SliverGridDelegate gridDelegate,
		required NullableIndexedWidgetBuilder itemBuilder,
		ChildIndexGetter? findChildIndexCallback,
		int? itemCount,
		bool addAutomaticKeepAlives = true,
		bool addRepaintBoundaries = true,
		bool addSemanticIndexes = true,
		double? cacheExtent,
		int? semanticChildCount,
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		ScrollViewKeyboardDismissBehavior keyboardDismissBehavior = ScrollViewKeyboardDismissBehavior.manual,
		String? restorationId,
		Clip clipBehavior = Clip.hardEdge,
		HitTestBehavior hitTestBehavior = HitTestBehavior.opaque
	})
	```

	等价于使用`SliverGridDelegateWithFixedCrossAxisCount`的快捷实现；

	```dart
	GridView.count({
		Key? key,
		Axis scrollDirection = Axis.vertical,
		bool reverse = false, 
		ScrollController? controller,
		bool? primary,
		ScrollPhysics? physics,
		bool shrinkWrap = false,
		EdgeInsetsGeometry? padding,
		required int crossAxisCount,
		double mainAxisSpacing = 0.0,
		double crossAxisSpacing = 0.0,
		double childAspectRatio = 1.0,
		bool addAutomaticKeepAlives = true,
		bool addRepaintBoundaries = true,
		bool addSemanticIndexes = true,
		double? cacheExtent,
		List<Widget> children = const <Widget>[],
		int? semanticChildCount,
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		ScrollViewKeyboardDismissBehavior keyboardDismissBehavior = ScrollViewKeyboardDismissBehavior.manual,
		String? restorationId,
		Clip clipBehavior = Clip.hardEdge,
		HitTestBehavior hitTestBehavior = HitTestBehavior.opaque
	})
	```

	等价于使用`SliverGridDelegateWithMaxCrossAxisExtent`的快捷实现；

	```dart
	GridView.extent({
		Key? key,
		Axis scrollDirection = Axis.vertical,
		bool reverse = false,
		ScrollController? controller,
		bool? primary,
		ScrollPhysics? physics,
		bool shrinkWrap = false,
		EdgeInsetsGeometry? padding,
		required double maxCrossAxisExtent,
		double mainAxisSpacing = 0.0,
		double crossAxisSpacing = 0.0,
		double childAspectRatio = 1.0,
		bool addAutomaticKeepAlives = true,
		bool addRepaintBoundaries = true,
		bool addSemanticIndexes = true,
		double? cacheExtent, List<Widget> children = const <Widget>[],
		int? semanticChildCount,
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		ScrollViewKeyboardDismissBehavior keyboardDismissBehavior = ScrollViewKeyboardDismissBehavior.manual,
		String? restorationId,
		Clip clipBehavior = Clip.hardEdge,
		HitTestBehavior hitTestBehavior = HitTestBehavior.opaque
	})
	```
* 属性
	* gridDelegate → SliverGridDelegate：用于创建网格的代理。

### Hero —— hero动画/共享元素过渡动画

在两个页面之间共同存在的内容的动画。

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > Hero
* 构造函数
	```dart
	Hero({
		Key? key,
		required Object tag,
		CreateRectTween? createRectTween,
		HeroFlightShuttleBuilder? flightShuttleBuilder,
		HeroPlaceholderBuilder? placeholderBuilder,
		bool transitionOnUserGestures = false,
		required Widget child
	})
	```
* 属性
	* tag → Object：必需前后一致，这样框架才知道要过渡那个组件。

### Icon —— 图标

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > Icon
* 构造函数
	```dart
	Icon(
		IconData? icon,
		{
		Key? key,
		double? size,
		double? fill,
		double? weight,
		double? grade,
		double? opticalSize,
		Color? color,
		List<Shadow>? shadows,
		String? semanticLabel,
		TextDirection? textDirection,
		bool? applyTextScaling
		}
	)
	```

### IconData —— 图标数据

* 构造函数
	```dart
	IconData(
		int codePoint,
		{
		String? fontFamily,
		String? fontPackage,
		bool matchTextDirection = false,
		List<String>? fontFamilyFallback
		}
	)
	```

### IgnorePointer —— 忽略指向事件

不接收事件，但子组件可以接收。

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > SingleChildRenderObjectWidget > IgnorePointer
* 构造函数
	```dart
	IgnorePointer({
		Key? key,
		bool ignoring = true,
		@Deprecated(
			'Use ExcludeSemantics or create a custom ignore pointer widget instead. '
			'This feature was deprecated after v3.8.0-12.0.pre.'
		)
		bool? ignoringSemantics, Widget? child
	})
	```

### InheritedWidget —— 共享数据继承组件

* 继承
	Object > DiagnosticableTree > Widget > ProxyWidget > InheritedWidget
* 构造函数
	```dart
	InheritedWidget({Key? key, required Widget child})
	```
* 方法
	* updateShouldNotify(covariant InheritedWidget oldWidget) → bool：通知子组件共享数据是否发生改变。如返回true，子组件中的`didChangeDependencies`方法会被调用。

### Image —— 图片

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > Image
* 构造函数
	```dart
	Image({
		Key? key,
		required ImageProvider\<Object> image,
		ImageFrameBuilder? frameBuilder,
		ImageLoadingBuilder? loadingBuilder,
		ImageErrorWidgetBuilder? errorBuilder,
		String? semanticLabel,
		bool excludeFromSemantics = false,
		double? width,
		double? height,
		Color? color,
		Animation<double>? opacity,
		BlendMode? colorBlendMode,
		BoxFit? fit,
		AlignmentGeometry alignment = Alignment.center,
		ImageRepeat repeat = ImageRepeat.noRepeat,
		Rect? centerSlice,
		bool matchTextDirection = false,
		bool gaplessPlayback = false,
		bool isAntiAlias = false,
		FilterQuality filterQuality = FilterQuality.medium
	})
	```
	
	从资源文件夹（于pubspec.yaml中的`flutter.assets`配置）加载图片：

	```dart
	Image.asset(
		String name,
		{
		Key? key,
		AssetBundle? bundle,
		ImageFrameBuilder? frameBuilder,
		ImageErrorWidgetBuilder? errorBuilder,
		String? semanticLabel,
		bool excludeFromSemantics = false,
		double? scale,
		double? width,
		double? height,
		Color? color,
		Animation<double>? opacity,
		BlendMode? colorBlendMode,
		BoxFit? fit,
		AlignmentGeometry alignment = Alignment.center,
		ImageRepeat repeat = ImageRepeat.noRepeat,
		Rect? centerSlice,
		bool matchTextDirection = false,
		bool gaplessPlayback = false,
		bool isAntiAlias = false,
		String? package,
		FilterQuality filterQuality = FilterQuality.medium,
		int? cacheWidth,
		int? cacheHeight
	})
	```
	
	从文件加载图片：

	```dart
	Image.file(
		File file,
		{
		Key? key,
		double scale = 1.0,
		ImageFrameBuilder? frameBuilder,
		ImageErrorWidgetBuilder? errorBuilder,
		String? semanticLabel,
		bool excludeFromSemantics = false,
		double? width,
		double? height,
		Color? color,
		Animation<double>? opacity,
		BlendMode? colorBlendMode,
		BoxFit? fit,
		AlignmentGeometry alignment = Alignment.center,
		ImageRepeat repeat = ImageRepeat.noRepeat,
		Rect? centerSlice,
		bool matchTextDirection = false,
		bool gaplessPlayback = false,
		bool isAntiAlias = false,
		FilterQuality filterQuality = FilterQuality.medium,
		int? cacheWidth,
		int? cacheHeight
	})
	```
	
	从网络加载图片：

	```dart
	Image.network(
		String src,
		{
		Key? key,
		double scale = 1.0,
		ImageFrameBuilder? frameBuilder,
		ImageLoadingBuilder? loadingBuilder,
		ImageErrorWidgetBuilder? errorBuilder,
		String? semanticLabel,
		bool excludeFromSemantics = false,
		double? width,
		double? height,
		Color? color,
		Animation<double>? opacity,
		BlendMode? colorBlendMode,
		BoxFit? fit,
		AlignmentGeometry alignment = Alignment.center,
		ImageRepeat repeat = ImageRepeat.noRepeat,
		Rect? centerSlice,
		bool matchTextDirection = false,
		bool gaplessPlayback = false,
		FilterQuality filterQuality = FilterQuality.medium,
		bool isAntiAlias = false,
		Map<String, String>? headers,
		int? cacheWidth,
		int? cacheHeight
		}
	)
	```
* 属性
	* height → double?：高度，单位为像素。
	* repeat → ImageRepeat：图片重复方式。
	* width → double?：宽度，单位为像素。

### LeafRenderObjectElement —— 无子组件组件的元素

### LeafRenderObjectWidget —— 无子组件组件

### Listener —— 事件监听器

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > SingleChildRenderObjectWidget > Listener
* 构造函数
	```dart
	Listener({
		Key? key,
		PointerDownEventListener? onPointerDown,
		PointerMoveEventListener? onPointerMove,
		PointerUpEventListener? onPointerUp,
		PointerHoverEventListener? onPointerHover,
		PointerCancelEventListener? onPointerCancel,
		PointerPanZoomStartEventListener? onPointerPanZoomStart,
		PointerPanZoomUpdateEventListener? onPointerPanZoomUpdate,
		PointerPanZoomEndEventListener? onPointerPanZoomEnd,
		PointerSignalEventListener? onPointerSignal,
		HitTestBehavior behavior = HitTestBehavior.deferToChild,
		Widget? child
	})
	```
* 属性
	* behavior → HitTestBehavior：当多个组件重叠时的行为。

### ListView —— 滚动列表视图

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > ScrollView > BoxScrollView > ListView
* 构造函数

	在有足够简单的子组件的情况下使用：

	```dart
	ListView({
		Key? key,
		Axis scrollDirection = Axis.vertical,
		bool reverse = false,
		ScrollController? controller,
		bool? primary,
		ScrollPhysics? physics,
		bool shrinkWrap = false,
		EdgeInsetsGeometry? padding,
		double? itemExtent,
		ItemExtentBuilder? itemExtentBuilder,
		Widget? prototypeItem,
		bool addAutomaticKeepAlives = true,
		bool addRepaintBoundaries = true,
		bool addSemanticIndexes = true,
		double? cacheExtent,
		List<Widget> children = const <Widget>[],
		int? semanticChildCount,
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		ScrollViewKeyboardDismissBehavior keyboardDismissBehavior = ScrollViewKeyboardDismissBehavior.manual,
		String? restorationId,
		Clip clipBehavior = Clip.hardEdge,
		HitTestBehavior hitTestBehavior = HitTestBehavior.opaque
	})
	```
	
	* addAutomaticKeepAlives：是否将每个元素包裹在`AutomaticKeepAlive`中，令子组件超出屏幕时不会被垃圾回收。默认为true。
	* addRepaintBoundaries：是否将每个元素包裹在`RepaintBoundary`中，令元素在第一次显示时被记住，即使滚出屏幕后再显示也无需重新绘制。
	
	内部使用`IndexedWidgetBuilder`，子组件仅在显示时才会被创建，即懒加载，适合在子组件较多的情况下使用：
	
	```dart
	ListView.builder({
		Key? key,
		Axis scrollDirection = Axis.vertical,
		bool reverse = false,
		ScrollController? controller,
		bool? primary,
		ScrollPhysics? physics,
		bool shrinkWrap = false,
		EdgeInsetsGeometry? padding,
		double? itemExtent,
		ItemExtentBuilder? itemExtentBuilder,
		Widget? prototypeItem,
		required NullableIndexedWidgetBuilder itemBuilder,
		ChildIndexGetter? findChildIndexCallback,
		int? itemCount,
		bool addAutomaticKeepAlives = true,
		bool addRepaintBoundaries = true,
		bool addSemanticIndexes = true,
		double? cacheExtent,
		int? semanticChildCount,
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		ScrollViewKeyboardDismissBehavior keyboardDismissBehavior = ScrollViewKeyboardDismissBehavior.manual,
		String? restorationId,
		Clip clipBehavior = Clip.hardEdge,
		HitTestBehavior hitTestBehavior = HitTestBehavior.opaque
	})
	```
	
	* itemCount：元素数量。null为无限。
	* itemBuilder：元素创建器。
	
	增加元素间的分隔线：

	```dart
	ListView.separated({
		Key? key,
		Axis scrollDirection = Axis.vertical,
		bool reverse = false,
		ScrollController? controller,
		bool? primary,
		ScrollPhysics? physics,
		bool shrinkWrap = false,
		EdgeInsetsGeometry? padding,
		required NullableIndexedWidgetBuilder itemBuilder,
		ChildIndexGetter? findChildIndexCallback,
		required IndexedWidgetBuilder separatorBuilder,
		required int itemCount,
		bool addAutomaticKeepAlives = true,
		bool addRepaintBoundaries = true,
		bool addSemanticIndexes = true,
		double? cacheExtent,
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		ScrollViewKeyboardDismissBehavior keyboardDismissBehavior = ScrollViewKeyboardDismissBehavior.manual,
		String? restorationId,
		Clip clipBehavior = Clip.hardEdge,
		HitTestBehavior hitTestBehavior = HitTestBehavior.opaque
	})
	```
	
	* separatorBuilder：分隔线创建器。
	
	使用`SliverChildDelegate`提供自定义子组件的额外属性：

	```dart
	ListView.custom({
		Key? key,
		Axis scrollDirection = Axis.vertical,
		bool reverse = false,
		ScrollController? controller,
		bool? primary,
		ScrollPhysics? physics,
		bool shrinkWrap = false,
		EdgeInsetsGeometry? padding,
		double? itemExtent,
		Widget? prototypeItem,
		ItemExtentBuilder? itemExtentBuilder,
		required SliverChildDelegate childrenDelegate,
		double? cacheExtent,
		int? semanticChildCount,
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		ScrollViewKeyboardDismissBehavior keyboardDismissBehavior = ScrollViewKeyboardDismissBehavior.manual,
		String? restorationId,
		Clip clipBehavior = Clip.hardEdge,
		HitTestBehavior hitTestBehavior = HitTestBehavior.opaque
	})
	```
* 属性
	* cacheExtent → double?：超出屏幕的子组件的预载数量。
	* childrenDelegate → SliverChildDelegate：用于创建子组件列表的代理。
	* controller → ScrollController?：滚动控制器。
	* itemExtent → double?：列表元素的大小。对垂直滚动的列表为元素高度，对水平滚动的列表为元素宽度。为了优化性能，通常会指定该值，而不是让框架自己计算。
	* shrinkWrap → bool：是否根据所有子组件的长度来计算自身长度。默认为false。通常`ListView`应沿滚动方向占用尽可能多的空间，仅当其处于一个相同滚动方向且无边界的父组件中并用于处理某些滚动冲突时，设置为true。

### Localizations —— 多语言本地化

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > Localizations
* 构造函数
	```dart
	Localizations({
		Key? key,
		required Locale locale,
		required List<LocalizationsDelegate> delegates,
		Widget? child
	})
	```
* 静态方法
	* localeOf(BuildContext context) → Locale：获取上下文中的语系区域。
	* of\<T>(BuildContext context, Type type) → T?：获取上下文中的多语言本地化数据源。

### LocalizationsDelegate\<T> —— 多语言本地化代理

* 方法
	* isSupported(Locale locale) → bool：是否支持指定的语系区域。
	* load(Locale locale) → Future\<T>：当语系区域改变时被调用，加载对应的数据。
	* shouldReload(covariant LocalizationsDelegate\<T> old) → bool：当组件的`build`方法被调用时，是否重新加载语系资源。通常为false。

### MultiChildRenderObjectWidget —— 多子组件组件

* 属性
	* children → List\<Widget>：子组件列表。

### MultiChildRenderObjectElement —— 多子组件组件的元素

### Navigator —— 页面跳转导航

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > Navigator
* 构造函数
	```dart
	Navigator({
		Key? key,
		List<Page> pages = const <Page<dynamic>>[],
		@Deprecated(
			'Use onDidRemovePage instead. '
			'This feature was deprecated after v3.16.0-17.0.pre.'
		)
		PopPageCallback? onPopPage,
		String? initialRoute,
		RouteListFactory onGenerateInitialRoutes = Navigator.defaultGenerateInitialRoutes,
		RouteFactory? onGenerateRoute,
		RouteFactory? onUnknownRoute,
		TransitionDelegate transitionDelegate = const DefaultTransitionDelegate<dynamic>(),
		bool reportsRouteUpdateToEngine = false,
		Clip clipBehavior = Clip.hardEdge,
		List<NavigatorObserver> observers = const <NavigatorObserver>[],
		bool requestFocus = true,
		String? restorationScopeId,
		TraversalEdgeBehavior routeTraversalEdgeBehavior = kDefaultRouteTraversalEdgeBehavior,
		DidRemovePageCallback? onDidRemovePage
	})
	```
* 静态方法
	* pop\<T extends Object?>(BuildContext context, [T? result]) → void：退出当前页面，返回上一个页面。
	* push\<T extends Object?>(BuildContext context, Route\<T> route) → Future\<T?>：跳转动态定义的页面。
	* pushNamed\<T extends Object?>(BuildContext context, String routeName, {Object? arguments}) → Future\<T?>：跳转静态定义的页面。

### Notification —— 通知

* 方法
	* dispatch(BuildContext? target) → void：分发通知。

### NotificationListener\<T extends Notification> —— 通知监听器

通知会逐层上报。

* 继承
	Object > DiagnosticableTree > Widget > ProxyWidget > NotificationListener
* 构造函数
	```dart
	NotificationListener({
		Key? key,
		required Widget child,
		NotificationListenerCallback<T>? onNotification
	})
	```
* 属性
	* onNotification → NotificationListenerCallback\<T>?：通知回调。

### Padding —— 内边距

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > SingleChildRenderObjectWidget > Padding
* 构造函数
	```dart
	Padding({
		Key? key,
		required EdgeInsetsGeometry padding,
		Widget? child
	})
	```
* 属性
	* child → Widget?：子组件。
	* padding → EdgeInsetsGeometry：边距。

### PerformanceOverlay —— 性能图表

显示GPU和CPU的性能图表。二者的理想状态是每秒60帧，即约16毫秒刷新一帧。

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > LeafRenderObjectWidget > PerformanceOverlay
* 构造函数
	```dart
	PerformanceOverlay.allEnabled({Key? key})
	```

### Positioned —— 定位

* 继承
	Object > DiagnosticableTree > Widget > ProxyWidget > ParentDataWidget\<StackParentData> > Positioned
* 构造函数
	```dart
	Positioned({
		Key? key,
		double? left,
		double? top,
		double? right,
		double? bottom,
		double? width,
		double? height,
		required Widget child
	})
	```
	`left`、`right`、`width`只可使用其二；`top`、`bottom`、`height`只可使用其二。
* 属性
	* bottom → double?：距离父组件的底部距离。
	* height → double?：子组件的高度。
	* left → double?：距离父组件的左侧距离。
	* right → double?：距离父组件的右侧距离。
	* top → double?：距离父组件的顶部距离。
	* width → double?：子组件的宽度。

### Route\<T> —— 路由

* 构造函数
	```dart
	Route({RouteSettings? settings})
	```

### Row —— 行布局/水平布局

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > MultiChildRenderObjectWidget > Flex > Row
* 构造函数
	```dart
	Row({
		Key? key,
		MainAxisAlignment mainAxisAlignment = MainAxisAlignment.start,
		MainAxisSize mainAxisSize = MainAxisSize.max,
		CrossAxisAlignment crossAxisAlignment = CrossAxisAlignment.center,
		TextDirection? textDirection,
		VerticalDirection verticalDirection = VerticalDirection.down,
		TextBaseline? textBaseline,
		List<Widget> children = const <Widget>[]
	})
	```
* 属性
	* children → List\<Widget>：子组件列表。
	* crossAxisAlignment → CrossAxisAlignment：交叉轴对齐方式。
	* mainAxisAlignment → MainAxisAlignment：主轴对齐方式。当`mainAxisSize`为`MainAxisSize.min`时，本属性不起作用，因为空间已被占满。
	* mainAxisSize → MainAxisSize：主轴所占空间大小。默认为MainAxisSize.max。
	* textDirection → TextDirection?：子组件的水平排布方向。默认跟随系统，大多为`TextDirection.ltr`。
	* verticalDirection → VerticalDirection：子组件的垂直排布方向。默认为`VerticalDirection.down`。

### ScrollController —— 滚动控制器

* 继承
	Object > ChangeNotifier > ScrollController
* 构造函数
	```dart
	ScrollController({
		double initialScrollOffset = 0.0,
		bool keepScrollOffset = true,
		String? debugLabel,
		ScrollControllerCallback? onAttach,
		ScrollControllerCallback? onDetach
	})
	```
* 方法
	* addListener(VoidCallback listener) → void：添加事件监听器。
	* animateTo(double offset, {required Duration duration, required Curve curve}) → Future\<void>：使用动画滚动。
	* jumpTo(double value) → void：不适用动画滚动。

### SingleChildRenderObjectWidget —— 单子组件组件

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > SingleChildRenderObjectWidget
* 属性
	* child → Widget?：子组件。

### SingleChildRenderObjectElement —— 单子组件组件的元素

### SingleChildScrollView —— 单个子组件的滚动视图

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > SingleChildScrollView
* 构造函数
	```dart
	SingleChildScrollView({
		Key? key,
		Axis scrollDirection = Axis.vertical,
		bool reverse = false,
		EdgeInsetsGeometry? padding,
		bool? primary,
		ScrollPhysics? physics,
		ScrollController? controller,
		Widget? child,
		DragStartBehavior dragStartBehavior = DragStartBehavior.start,
		Clip clipBehavior = Clip.hardEdge,
		HitTestBehavior hitTestBehavior = HitTestBehavior.opaque,
		String? restorationId,
		ScrollViewKeyboardDismissBehavior keyboardDismissBehavior = ScrollViewKeyboardDismissBehavior.manual
	})
	```
* 属性
	* child → Widget?：子组件。
	* controller → ScrollController?：滚动控制器。
	* padding → EdgeInsetsGeometry?：外边距。
	* physics → ScrollPhysics?：滚动到尽头后继续滚动的物理效果。
	* primary → bool?：是否是主滑块。通常在嵌套其它滚动组件时使用。默认为true。
	* reverse → bool：是否反向滚动。默认为false。
	* scrollDirection → Axis：滚动的方向。

### SizedBox —— 尺寸盒

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > SingleChildRenderObjectWidget > SizedBox
* 构造函数
	```dart
	SizedBox({
		Key? key,
		double? width,
		double? height,
		Widget? child
	})
	```

### SliverChildBuilderDelegate —— Sliver家族的子组件创建器代理

* 继承
	Object > SliverChildDelegate > SliverChildBuilderDelegate
* 构造函数
	```dart
	SliverChildBuilderDelegate(
		NullableIndexedWidgetBuilder builder,
		{
		ChildIndexGetter? findChildIndexCallback,
		int? childCount,
		bool addAutomaticKeepAlives = true,
		bool addRepaintBoundaries = true,
		bool addSemanticIndexes = true,
		SemanticIndexCallback semanticIndexCallback = _kDefaultSemanticIndexCallback,
		int semanticIndexOffset = 0
	})
	```
* 方法
	* didFinishLayout(int firstIndex, int lastIndex) → void：再绘制完组件时被调用。

### SliverFixedExtentList —— Sliver家族的固定尺寸列表

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > SliverWithKeepAliveWidget > SliverMultiBoxAdaptorWidget > SliverFixedExtentList
* 构造函数
	```dart
	SliverFixedExtentList({
		Key? key,
		required SliverChildDelegate delegate,
		required double itemExtent
	})
	```

### SliverGrid —— Sliver家族的网格

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > SliverWithKeepAliveWidget > SliverMultiBoxAdaptorWidget > SliverGrid
* 构造函数
	```dart
	SliverGrid({
		Key? key,
		required SliverChildDelegate delegate,
		required SliverGridDelegate gridDelegate
	})
	```

### Stack —— 栈布局/堆叠布局

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > MultiChildRenderObjectWidget > Stack
* 构造函数
	```dart
	Stack({
		Key? key,
		AlignmentGeometry alignment = AlignmentDirectional.topStart,
		TextDirection? textDirection,
		StackFit fit = StackFit.loose,
		Clip clipBehavior = Clip.hardEdge,
		List<Widget> children = const <Widget>[]
	})
	```
* 属性
	* alignment → AlignmentGeometry：对齐。只对没有使用`Positioned`包裹的子组件或已使用`Positioned`包裹但没有充足定位信息的子组件使用。
	* children → List\<Widget>：子组件列表。
	* fit → StackFit：对没有定位信息的子组件的尺寸适应方式。
	* textDirection → TextDirection?：子组件的水平排布方向。默认跟随系统，大多为`TextDirection.ltr`。

### State\<T extends StatefulWidget> —— 状态

* 构造函数
	```dart
	State()
	```
* 方法
	* build(BuildContext context) → Widget：构建组件。会在一些其它方法（如：`setState`）后被自动调用。
	* deactivate() → void：从组件树中移除时被调用。
	* didChangeDependencies() → void：依赖变更时被调用。
	* didUpdateWidget(covariant T oldWidget) → void：重新构建组件时被调用。
	* dispose() → void：从组件树中永久销毁时被调用。
	* initState() → void：添加至组件树中时被调用。
	* reassemble() → void：热重载时被调用。
	* setState(VoidCallback fn) → void：设置状态。
* 属性
	* widget → T

### StatefulElement —— 有状态元素

* 继承
	Object > DiagnosticableTree > Element > ComponentElement > StatefulElement
* 构造函数
	```dart
	StatefulElement(StatefulWidget widget)
	```

### StatefulWidget —— 有状态组件

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget
* 构造函数
	```dart
	StatefulWidget({Key? key})
	```
* 方法
	* createElement() → StatefulElement：创建元素。
	* createState() → State\<StatefulWidget>：创建状态。

### StatelessElement —— 无状态元素

* 继承
	Object > DiagnosticableTree > Element > ComponentElement > StatelessElement
* 构造函数
	```dart
	StatelessElement(StatelessWidget widget)
	```

### StatelessWidget —— 无状态组件

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget
* 构造函数
	```dart
	StatelessWidget({Key? key})
	```
* 方法
	* createElement() → StatelessElement：创建元素。
	* build() → Widget：构建组件。

### Text —— 文本

* 继承
	Object > DiagnosticableTree > Widget > StatelessWidget > Text
* 构造函数
	```dart
	Text(
		String data, 
		{
		Key? key,
		TextStyle? style,
		StrutStyle? strutStyle,
		TextAlign? textAlign,
		TextDirection? textDirection,
		Locale? locale,
		bool? softWrap,
		TextOverflow? overflow,
		@Deprecated(
			'Use textScaler instead. '
			'Use of textScaleFactor was deprecated in preparation for the upcoming nonlinear text scaling support. '
			'This feature was deprecated after v3.12.0-2.0.pre.'
		)
		double? textScaleFactor,
		TextScaler? textScaler,
		int? maxLines,
		String? semanticsLabel,
		TextWidthBasis? textWidthBasis,
		TextHeightBehavior? textHeightBehavior,
		Color? selectionColor
		}
	)
	```
	
	```dart
	Text.rich(
		InlineSpan textSpan,
		{
		Key? key,
		TextStyle? style,
		StrutStyle? strutStyle,
		TextAlign? textAlign,
		TextDirection? textDirection,
		Locale? locale,
		bool? softWrap,
		TextOverflow? overflow,
		@Deprecated(
			'Use textScaler instead. '
			'Use of textScaleFactor was deprecated in preparation for the upcoming nonlinear text scaling support. '
			'This feature was deprecated after v3.12.0-2.0.pre.'
		)
		double? textScaleFactor,
		TextScaler? textScaler,
		int? maxLines,
		String? semanticsLabel,
		TextWidthBasis? textWidthBasis,
		TextHeightBehavior? textHeightBehavior,
		Color? selectionColor
		}
	)
	```
* 属性
	* maxLines → int?：显示的最大行数。默认位自动换行。通常和`overflow`结合使用。
	* overflow → TextOverflow?：文本超出显示范围时的省略方式。
	* style → TextStyle?：文本样式。
	* textAlign → TextAlign?：文本的对齐方式。

### TextEditingController —— 文本编辑控制器

* 继承
	Object > ChangeNotifier > ValueNotifier\<TextEditingValue> > TextEditingController
* 构造函数
	```dart
	TextEditingController({
		String? text
	})
	```
* 属性
	* text ↔ String：当前的文本内容。

### Transform —— 变换

无论怎样变换，组件的尺寸始终是未变换时的大小。

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > SingleChildRenderObjectWidget > Transform
* 构造函数

	矩阵变换：

	```dart
	Transform({
		Key? key,
		required Matrix4 transform,
		Offset? origin,
		AlignmentGeometry? alignment,
		bool transformHitTests = true,
		FilterQuality? filterQuality,
		Widget? child
	})
	```
	
	旋转变换：
	
	```dart
	Transform.rotate({
		Key? key,
		required double angle,
		Offset? origin,
		AlignmentGeometry? alignment = Alignment.center,
		bool transformHitTests = true,
		FilterQuality? filterQuality,
		Widget? child
	})
	```
	
	缩放变换：
	
	```dart
	Transform.scale({
		Key? key,
		double? scale,
		double? scaleX,
		double? scaleY,
		Offset? origin,
		AlignmentGeometry? alignment = Alignment.center,
		bool transformHitTests = true,
		FilterQuality? filterQuality,
		Widget? child
	})
	```
	
	平移变换：
	
	```dart
	Transform.translate({
		Key? key,
		required Offset offset,
		bool transformHitTests = true,
		FilterQuality? filterQuality,
		Widget? child
	})
	```

### Widget —— 组件

与交互有关的。

* 构造函数
	```dart
	Widget({Key? key})
	```

### WillPopScope —— 返回拦截

* 继承
	Object > DiagnosticableTree > Widget > StatefulWidget > WillPopScope
* 构造函数
	```dart
	WillPopScope({
		Key? key,
		required Widget child,
		required WillPopCallback? onWillPop
	})
	```
* 属性
	* child → Widget：弹出提示的组件。
	* onWillPop → WillPopCallback?：页面返回时的回调。默认的回调返回`Future.value(true)`，表示正常推出页面。如回调返回`Future.value(false)`则不退出，停留在当前页面。

### Wrap —— 流式布局

* 继承
	Object > DiagnosticableTree > Widget > RenderObjectWidget > MultiChildRenderObjectWidget > Wrap
* 构造函数
	```dart
	Wrap({
		Key? key,
		Axis direction = Axis.horizontal,
		WrapAlignment alignment = WrapAlignment.start,
		double spacing = 0.0,
		WrapAlignment runAlignment = WrapAlignment.start,
		double runSpacing = 0.0,
		WrapCrossAlignment crossAxisAlignment = WrapCrossAlignment.start,
		TextDirection? textDirection,
		VerticalDirection verticalDirection = VerticalDirection.down,
		Clip clipBehavior = Clip.none,
		List<Widget> children = const <Widget>[]
	})
	```
* 属性
	* alignment → WrapAlignment：主轴方向上子组件的对齐方式。
	* direction → Axis：子组件的排布方向。
	* runAlignment → WrapAlignment：交叉轴方向上子组件的对齐方式。
	* runSpacing → double：交叉轴方向上子组件之间的间距。
	* spacing → double：主轴方向上子组件之间的间距。

## 函数

### debugDumpApp —— 转储组件层

需至少构建过一次组件，且不能在`build`方法执行过程中调用。

```dart
void debugDumpApp()
```

转储信息实际上是把所有组件按树形结构罗列出来。如组件被标记为dirty，表示转储行为是通过该组件触发的。
