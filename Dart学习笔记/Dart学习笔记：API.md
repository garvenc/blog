本文更新于2024-12-25，使用Dart 2.18.2。

[TOC]

官方文档：[https://api.dart.dev](https://api.dart.dev)或[https://api.dart.cn](https://api.dart.cn)

# dart:async 【异步库】

```dart
import 'dart:async';
```

## 类

### Future\<T> —— 异步计算结果

* 构造函数
	```dart
	Future(FutureOr<T> computation())
	```
	
	延迟计算：
	
	```dart
	Future.delayed(Duration duration, [FutureOr<T> computation()?])
	```
* 方法
	* then\<R>(FutureOr\<R> onValue(T value), {Function? onError}) → Future\<R>：注册计算完成后的回调。

### FutureOr\<T> —— 同步或异步的计算结果

可表示`Future<T>`或`T`类型。

### Stream\<T> —— 流

* 方法
	* join([String separator = ""]) → Future\<String>：将流内容连接成字符串。

# dart:convert 【数据转换库】

```dart
import 'dart:convert';
```

## 类

### JsonCodec —— JSON编解码器

* 方法
	* decode(String source, {Object? reviver(Object? key, Object? value)?}) → dynamic：解码。
	* encode(Object? value, {Object? toEncodable(dynamic object)?}) → String：编码。

### Utf8Codec —— UTF-8编解码器

* 属性
	* decoder → Utf8Decoder：解码器。
	* encoder → Utf8Encoder：编码器。

## 常量

### json —— JSON编解码

### utf8 —— UTF-8编解码

# dart:core 【核心库】

自动导入。

## 类

### DateTime —— 日期时间

* 构造函数
	
	当前日期时间：
	
	```dart
	DateTime.now()
	```
* 属性
	* day → int：日。
	* hour → int：时。
	* minute → int：分。
	* month → int：月。
	* second → int：秒。
	* year → int：年。
* 方法
	* add(Duration duration) → DateTime：加。
	* subtract(Duration duration) → DateTime：减。

### double —— 浮点数

64位。

* 属性
	* isInfinite → bool：是否是正负无穷大。

### Duration —— 时长

* 构造函数
	```dart
	Duration({int days = 0, int hours = 0, int minutes = 0, int seconds = 0, int milliseconds = 0, int microseconds = 0})
	```

### int —— 整数

64位。

* 方法
	* toString() → String：转换成字符串。
* 静态方法
	* parse(String source, {int? radix}) → int：从字符串解析整数。

### List\<E> —— 列表

* 构造函数
	
	创建包含若干个元素的列表：
	
	```dart
	List.generate(int length, E generator(int index), {bool growable = true})
	```
* 属性
	* length ↔ int：长度。
* 方法
	* map\<T>(T toElement(E e)) → Iterable\<T>：对每个元素映射出新的元素。
* 运算符
	* operator +(List\<E> other) → List\<E>：加。和另一个列表连接成一个新列表。

### Map\<K, V> —— 映射

* 构造函数
	```dart
	Map()
	```
* 属性
	* length → int：长度。
* 方法
	* containsKey(Object? key) → bool：是否包含键。
	* remove(Object? key) → V?：删除键。

### Object —— 对象

* 属性
	* runtimeType → Type：运行时的对象类型。

### Set\<E> —— 集合

* 属性
	* length → int：长度。
* 方法
	* add(E value) → bool：添加元素。
	* addAll(Iterable\<E> elements) → void：添加所有多个元素。
	* clear() → void：清空所有元素。
	* contains(Object? value) → bool：是否包含元素。
	* containsAll(Iterable\<Object?> other) → bool：是否包含所有多个元素。
	* remove(Object? value) → bool：删除元素。
	* removeAll(Iterable\<Object?> elements) → void：删除所有多个元素。

### Uri —— URI地址

* 静态方法
	* parse(String uri, [int start = 0, int? end]) → Uri：从字符串解析。

# dart:developer 【开发库】

```dart
import 'dart:developer';
```

## 类

### Timeline —— 时间线

* 静态方法
	* finishSync() → void：结束同步。
	* startSync(String name, {Map? arguments, Flow? flow}) → void：开始同步。

# dart:io 【I/O库】

```dart
import 'dart:io';
```

## 类

### HttpClient —— HTTP客户端

* 构造函数
	```dart
	HttpClient({SecurityContext? context})
	```
* 方法
	* close({bool force = false}) → void：关闭。
	* getUrl(Uri url) → Future<HttpClientRequest>：以URL发送GET请求。

### HttpClientRequest —— HTTP客户端请求

* 属性
	* headers → HttpHeaders：请求头。
* 方法
	* close() → Future\<HttpClientResponse>：关闭请求，返回响应。

### HttpClientResponse —— HTTP客户端响应

* 方法
	* transform\<S>(StreamTransformer\<List\<int>, S> streamTransformer) → Stream\<S>：转换流。

### HttpHeaders —— HTTP头

* add(String name, Object value, {bool preserveHeaderCase = false}) → void：添加。

## 函数

### sleep —— 睡眠

```dart
void sleep( Duration duration )
```

# dart:math 【数学库】

```dart
import 'dart:math';
```

## 类

### Random —— 随机数

* 构造函数
	伪随机数：
	
	```dart
	Random([int? seed])
	```
	
	安全的随机数：
	
	```dart
	Random.secure()
	```
* 方法
	* nextInt(int max) → int：返回[0, max)区间的随机整数。

# dart:mirrors 【反射库】

```dart
import 'dart:mirrors';
```

## 类

### LibraryDependencyMirror —— 库依赖反射

* 方法
	* loadLibrary() → Future\<LibraryMirror>：加载库。
