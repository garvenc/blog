本文更新于2024-12-22，使用Dart 2.18.2。

[TOC]

可以使用`dart run FILENAME`运行程序文件。

# 关键字

* abstract
* as
* assert
* async
* await
* break
* case
* catch
* class
* const
* continue
* covariant
* default
* deferred
* do
* dynamic
* else
* enum
* export
* extends
* external
* factory
* false
* final
* finally
* for
* get
* hide
* if
* implements
* import
* in
* interface
* is
* library
* mixin
* new
* null
* on
* operator
* part
* rethrow
* return
* set
* show
* static
* super
* switch
* sync
* this
* throw
* true
* try
* typedef
* var
* void
* while
* with
* yield

# 常量

const声明的是编译时常量。

```dart
cosnt s1 = "";
const String s2 = "";

final s3 = "";
final String s4 = "";

var list = const[1, 2, 3];
const list = [1, 2, 3];

var dog = const Dog();
const dog = Dog();
```

# 变量

```dart
var a1;
var a2 = null;
dynamic a3;
dynamic a4 = "";

var s1 = "";
String s2 = "";
```

# 基本数据类型

## 数值

* int: 64位。范围为[-2^63 ~ 2^63-1]。编译为JavaScript时使用JavaScript的范围[-2^53 ~ 2^53-1]。
* double：64位。范围约为[-1.7E308 ~ 1.7E308]。

## 字符串

使用UTF-16编码。

```dart
var s1 = '\'';
var s2 = "\"";

var s3 = '''1
2''';
var s4 = """1
2""";

var s5 = "\u2665\u{1f47b}";

var a1 = '$s1 ${s2}';
var a2 = "$s1 ${s2}";
var a3 = s1 + " " + s2;
var a4 = s1 * 3;
var a5 = "${s1+s2}";
```

## 布尔

```dart
var b1 = true;
bool b2 = false;
```

## 列表

```dart
var l = [1, "2", 1];
```

## 集合

```dart
var s = {1, "2"};
```

## 映射

```dart
var m1 = {};
var m2 = {
	"a": 1,
	"b": "2"
};
```

# 运算符

## 运算符优先级

从高到低依次为：

| 描述         | 运算符                   |
| ----------- | ----------------------- |
| 一元后缀      | ++，--，()，[]，.，?.    |
| 一元前缀      | -，!，~，++，--          |
| 乘除         | *，/，~/，%              |
| 加减         | +，-                    |
| 移位         | <<，>>，>>>             |
| 按位与        | &                      |
| 按位异或      | ^                      |
| 按位或        | \|                     |
| 关系和类型测试 | >=，>，<=，<，as，is，is! |
| 相等判断      | ==，!=                  |
| 逻辑与       | &&                      |
| 逻辑或       | \|\|                    |
| 为空判断     | ??                      |
| 条件        | ? :                      |
| 级联        | ..                       |
| 赋值        | =，+=，-=，*=，/=，~/=，%=，<<=，>>=，>>>=，&=，^=，\|= |

## 算数运算符

| 运算符 | 用法         | 描述                   |
| ----- | ----------- | --------------------- |
| +     | 1 + 2       | 相加                   |
| -     | 1 - 2       | 相减                   |
| -     | -1          | 负数                   |
| *     | 1 * 2       | 相乘                   |
| /     | 1 / 2       | 相除（返回浮点数）        |
| ~/    | 1 ~/ 2      | 相除（返回整数）         |
| %     | 1 % 2       | 取余                   |
| ++    | ++n         | （前）自增              |
| ++    | n++         | （后）自增              |
| --    | --n         | （前）自减              |
| --    | n--         | （后）自减              |

## 关系运算符

| 运算符 | 用法         | 描述                   |
| ----- | ----------- | --------------------- |
| ==    | 1 == 2      | 相等                   |
| !=    | 1 != 2      | 不等                   |
| >     | 1 > 2       | 大于                   |
| >=    | 1 >= 2      | 大于等于                |
| <     | 1 < 2       | 小于                   |
| <=    | 1 <= 2      | 小于等于                |

## 类型判定运算符

| 运算符 | 用法         | 描述                        |
| ----- | ----------- | -------------------------- |
| as    | 1 as int    | 类型转换（结果为运算符后的类型） |
| is    | 1 is int    | 是此类型（结果为bool类型）     |
| is!   | 1 is! int   | 非此类型（结果为bool类型）     |

## 赋值运算符

| 运算符 | 用法         | 描述                   |
| ----- | ----------- | --------------------- |
| =     | n = 2       | 赋值                   |
| +=    | n += 2      | 即 n = n + 2           |
| -=    | n -= 2      | 即 n = n - 2           |
| *=    | n \*= 2     | 即 n = n * 2           |
| /=    | n /= 2      | 即 n = n / 2           |
| ~/=   | n ~/= 2     | 即 n = n ~/ 2          |
| %=    | n %= 2      | 即 n = n % 2           |
| <<=   | n <<= 2     | 即 n = n << 2          |
| >>=   | n >>= 2     | 即 n = n >> 2          |
| >>>=  | n >>>= 2    | 即 n = n >>> 2         |
| &=    | n &= 2      | 即 n = n & 2           |
| \|=   | n \|= 2     | 即 n = n \| 2          |
| ^=    | n ^= 2      | 即 n = n ^ 2           |

## 逻辑运算符

| 运算符 | 用法             | 描述                   |
| ----- | --------------- | --------------------- |
| !     | !true           | 非                     |
| \|\|  | true \|\| false | 或                     |
| &&    | true && false   | 与                     |

## 位运算符

| 运算符 | 用法             | 描述                   |
| ----- | --------------- | --------------------- |
| &     | 1 & 2           | 按位与                 |
| \|    | 1 \| 2          | 按位或                 |
| ^     | 1 ^ 2           | 按位异或                |
| ~     | ~1              | 按位取反（结果为相反数减一）    |
| <<    | 1 << 2          | 按位左移                     |
| >>    | 1 >> 2          | 有符号按位右移（左侧填充符号位） |
| >>>   | 1 >>> 2         | 无符号按位右移（左侧填充0）     |

## 条件运算符

| 运算符 | 用法             | 描述                   |
| ----- | --------------- | --------------------- |
| ? :   | v ? t : f       | 条件判断（如v为true取值t，否则取值f） |
| ??    | v ?? n          | 非空判断（如v非null取值v，否则取值n） |

## 访问运算符

| 运算符 | 用法           | 描述                   |
| ----- | ------------- | --------------------- |
| ()    | f()           | 函数调用                |
| []    | a[1]          | 使用索引访问元素          |
| .     | a.b           | 访问对象成员             |
| ?.    | a?.b          | 条件访问对象成员（如a非null取值b，否则取值null） |
| ..    | v..f1()..f2() | 级联运算（等价于v.f1();v.f2()，严格意义上并不是一个运算符，只是一个特殊语法） |

# 流程控制

## 条件语句

### if-else

```dart
if (condition1) {
} else if (condition2) {
} else {
}
```

### switch-case

```dart
switch (v) {
	case 1:
	case 2:
		print("");
		break;
	default:
}
```

## 循环语句

### for

```dart
for (var i = 0; i < n; i++) {
}
```

### for-in

```dart
for (var v in set) {
}
```

### while

```dart
while (condition) {
}
```

### do-while

```dart
do {
} while(condition);
```

## break语句

```dart
switch (v) {
	case 1:
	case 2:
		print("");
		break;
	default:
}

for (var i = 0; i < n; i++) {
	break;
	print(i);
}
```

## continue语句

```dart
for (var i = 0; i < n; i++) {
	if (i == 0) {
		continue;
	}
	print(i);
}
```

# 断言

断言只在开发（Checked）模式下有效，在生产（Production）模式下无效。

```dart
assert(flag);
```

# 异常

```dart
try {
	throw new Exception();
} on RangeError {
	rethrow;
} catch (e) {
} finally {
}
```

# 函数

```dart
String join(String a, String b) {
	return a + b;
}

add(a, b) {
	return a + b;
}

run() {
}
```

```dart
var s = join("a", "b");
var n = add(1, 2);
var a = run();
```

## 箭头函数

```dart
String join(String a, String b) => a + b;
```

## 可选位置参数

```dart
String join(String a, String b, [String c = "", String d = ""]) {
	return a + b + c + d;
}
```

```dart
var s = join("a", "b", "c");
```

## 可选命名参数

```dart
String join(String a, String b, {String c: "", d: ""}) {
	return a + b + c + d;
}
```

```dart
var s = join("a", "b", c: "c");
```

## 闭包

```dart
Function hello(String name) {
	var say = () {
		print("Hello $name");
	};
	return say;
}
```

```dart
var say = hello("Tom");
say();
```

## 主函数

```dart
void main(List<String> args) {
}
```

# 类

## 成员变量/实例变量

```dart
class Person {
	String name = "";
	int age = 0;
}
```

### getter和setter

会为每个成员变量自动生成getter方法，对于非final修饰的成员变量也会自动生成setter方法。

```dart
class Person {
	String name = "";
	
	String get Name {
		return name;
	}

	set Name(String n) {
		name = n;
	}
}
```

```dart
var p = new Person();
p.Name = "Tom";
print(p.Name);
```

## 静态变量/类变量

```dart
class Person {
	String name = "";
	int age = 0;

	static int total = 1000;
	static Person me = new Person();
}
```

```dart
print(Person.total);
```

## 构造函数

构造函数的执行顺序为：实例变量赋值 -> 初始化参数列表 -> 父类构造函数 -> 本类构造函数。

### 默认无参构造函数

```dart
class Person {
	String name = "";
	int age = 0;
}
```

```dart
var p1 = new Person();
var p2 = Person();
```

默认自动生成无参构造函数：

```dart
	Person() {
	}
```

### 显式声明构造函数

```dart
class Person {
	String name = "";
	int age = 0;
	
	Person(String name, int age) {
		this.name = name;
		this.age = age;
	}
}
```

```dart
var p = new Person("Tom", 10);
```

### 命名构造函数

```dart
class Person {
	String name = "";
	int age = 0;
	
	Person.myself(age) {
		name = "Tom";
		this.age = age;
	}
}
```

```dart
var p = new Person.myself(10);
```

### 构造函数简写

```dart
class Person {
	String name = "";
	int age = 0;
	
	Person(this.name, this.age);
	
	Person.create(this.name, {this.age: 0});
}

class Student extends Person {
	String number = "";

	Student(super.name, super.age);
}
```

```dart
var p1 = new Person("Tom", 10);
var p2 = new Person.create("Tom", age: 10);

var s = new Student("Tom", 10);
```

### 初始化列表

```dart
class Person {
	String name = "";
	int age = 0;
	
	Person.myself(int age): name = "Tom", this.age = age {
	}
}
```

```dart
var p = new Person.myself(10);
```

### 重定向构造函数

```dart
class Person {
	String name = "";
	int age = 0;
	
	Person(this.name, this.age);
	
	Person.myself(int age): this("Tom", age);
}
```

```dart
var p = new Person.myself(10);
```

### 常量构造函数

```dart
class Person {
	final String name;
	final int age;
	
	const Person(this.name, this.age);
}
```

```dart
var p1 = new Person("Tom", 10);
var p2 = const Person("Tom", 10);
```

### 工厂方法的构造函数

```dart
class Person {
	String name = "";
	int age = 0;
	
	static Map<String, Person> _people = {};
	
	Person(this.name, this.age);
	
	factory Person.Tom() {
		var name = "Tom";
		var p = _people[name];
		if (p == null) {
			p = Person(name, 0);
			_people[name] = p;
		}
		return p;
	}
}
```

```dart
var p = new Person.Tom();
```

### 自动调用父类无参构造函数

```dart
class Person {
	String name = "";
	int age = 0;
}

class Student extends Person {
	String number = "";
}
```

```dart
var s = new Student();
```

### 显式调用父类构造函数

```dart
class Person {
	String name = "";
	int age = 0;
	
	Person(this.name, age);
	
	Person.myself(int age) {
		name = "Tom";
		this.age = age;
	}
}

class Student extends Person {
	String number = "";

	Student(String name, int age, String number): super(name, age) {
		this.number = number;
	}

	Student.myself(int age, String number): this.number = number, super.myself(age) {
	}
}
```

```dart
var s1 = new Student("Tom", 10, "no.1");
var s2 = new Student.myself(10);
```

## 成员方法/实例方法

```dart
class Person {
	String name = "";
	int age = 0;

	String info() {
		return "Person name is $name and age is $age.";
	}
}
```

```dart
var s = new Student();
s.info();
```

## 静态方法/类方法

```dart
class Person {
	String name = "";
	int age = 0;

	static readme() {
		print("Person!");
	}
}
```

```dart
Person.readme();
```

## 继承

不支持多继承。

```dart
class Person {
	String name = "";
	int age = 0;
	
	String info() {
		return "Person name is $name and age is $age.";
	}
}

class Student extends Person {
	String number = "";
}
```

```dart
var s = new Student();
s.info();
```

### 函数重写

```dart
class Person {
	String name = "";
	int age = 0;
	
	String info() {
		return "Person name is $name and age is $age.";
	}
}

class Student extends Person {
	String number = "";
	
	@override
	String info() {
		return super.info() + " Student number is $number.";
	}
}
```

### 运算符重写

```dart
class Person {
	String name = "";
	int age = 0;
	
	Person(this.name, this.age);
		
	Person operator +(Person p) {
		var name = this.name + " and " + p.name;
		var age = this.age + p.age;
		return new Person(name, age);
	}
}
```

```dart
var p1 = new Person("Tom", 10);
var p2 = new Person("Jerry", 9);
var p = p1 + p2;
```

可重写的运算符有：

* <
* <=
* >
* >=
* ==
* +
* -
* *
* /
* ~/
* %
* &
* |
* ^
* ~
* <<
* >>
* []
* []=

### 抽象类与抽象方法

抽象类无法被实例化。

```dart
abstract class Person {
	String name = "";
	int age = 0;
	
	String info();
}

class Student extends Person {
	String number = "";
	
	@override
	String info() {
		return "Person name is $name and age is $age. Student number is $number.";
	}
}
```

## 接口

```dart
class Person {
	String name = "";
	int age = 0;
}

abstract class Say {
	int voice = 0;

	void say();
}

class Hello {
	void hello() {
		print("hello!");
	}
}

class Student extends Person implements Say, Hello {
	String number = "";

	@override
	int voice = 0;

	@override
	void say() {
		print("Person name is $name and age is $age. Student number is $number.");
	}

	@override
	void hello() {
		print("hello!");
		say();
	}
}
```

## Mixin特性

```dart
class Person {
	String name = "";
	int age = 0;
}

class Run {
	void run() {
		print("run!");
	}
}

abstract class Say {
	void say();
}

class Student extends Person with Run implements Say {
	String number = "";

	@override
	void say() {
		print("Person name is $name and age is $age. Student number is $number.");
	}
}
```

```dart
var s = new Student();
s.run();
```

## 可调用的类

```dart
class Run {
	call() {
		print("run");
	}
}

class Say {
	call(String a, String b) {
		print("say $a $b");
	}
}
```

```dart
var run = new Run();
run();

var say = new Say();
say("a", "b");
```

# 枚举

无法继承，无法使用Mixin特性，无法实例化。

```dart
enum Grade {
	grade1,
	grade2
}
```

```dart
var grade = Grade.grade1;
var index = Grade.grade1.index;
```

# 类型定义

```dart
typedef Repeat = String Function(String str);
typedef Name = String;
```

```dart
Name name = "Tom";
Repeat f = (str) {
  return str + ", " + str;
};
var s = f(name);
```

# 泛型

## 函数泛型

```dart
String add<T>(T a, T b) {
	return a.toString() + b.toString();
}

String addString<T extends String>(T a, T b) {
	return a + b;
}
```

```dart
add(1, 2);
addString("a", "b");
```

## 类泛型

```dart
class Add<T> {
	String add(T a, T b) {
		return a.toString() + b.toString();
	}
}

class AddString<T extends String> {
	String add(T a, T b) {
		return a + b;
	}
}
```

```dart
new Add().add(1, 2);
new AddString().add("a", "b");
```

# 异步

异步本质上是使用isolates运行模式进行并发。

```dart
void main(List<String> args) {
	work();
	print(2);
}

work() async {
	print(1);
	var str = await takeTime();
	print(4);
	print(str);
}

Future<String> takeTime() async {
	var str = await new Future.delayed(new Duration(seconds: 2), () {
		print("...");
		print(3);
		return "hello";
	});
	return str;
}
```

## 在循环中异步

```dart
void main(List<String> args) async {
	Stream<String> stream = Stream.fromFuture(new Future.delayed(new Duration(seconds: 2), () {
		print("...");
		print(2);
		return "hello";
	}));
	print(1);
	await for (var str in stream) {
			print(str);
	}
	print(3);
}
```

# 注解/元数据

## @deprecated

弃用。

```dart
@deprecated
// Should use Hello();
void hello() {
}
```

## @override

重写。

```dart
class Person {
	String name = "";
}

class Student extends Person {
	@override
	String name = "";
}
```

## @proxy

# 注释

## 单行注释

```dart
// Comment.
```

## 多行注释

```dart
/*
Comment.
Comment.
Comment.
*/

/* Comment.
Comment.
Comment. */
```

## 文档注释

```dart
/**
 * This is a demo.
 * 
 * Refer [say] for detail.
 */
void hello() {
}

/// This is a demo.
/// Used by [hello].
void say() {
}
```

# 库

标识符除非以“_”开头，否则都是对库外可见的。

## import内置库

```dart
import 'dart:math';

void main(List<String> args) {
  print(pi);
}
```

## import包库

```dart
import 'package:flutter/material.dart';
```

## import本地库

lib.dart：

```dart
var _name = "lib.dart";

class Hello {
	void hello() {
		print("hello " + _name);
	}
}
```

main.dart：

```dart
import 'lib.dart';

void main(List<String> args) {
	new Hello().hello();
}
```

## import-as

src/liba.dart：

```dart
class Hello {
	void helloA() {
		print("hello a");
	}
}
```

src/libb.dart：

```dart
class Hello {
	void helloB() {
		print("hello b");
	}
}
```

main.dart：

```dart
import 'src/liba.dart' as liba;
import 'src/libb.dart' as libb;

void main(List<String> args) {
	new liba.Hello().helloA();
	new libb.Hello().helloB();
}
```

## import-show

lib.dart：

```dart
class Hello {
}

class Say {
}

class Run {
}
```

main.dart：

```dart
import 'lib.dart' show Hello, Say;

void main(List<String> args) {
	new Hello();
	new Say();
}
```

## import-hide

lib.dart：

```dart
class Hello {
}

class Say {
}

class Run {
}
```

main.dart：

```dart
import 'lib.dart' hide Run;

void main(List<String> args) {
	new Hello();
	new Say();
}
```

## import-deferred as懒加载

lib.dart：

```dart
class Hello {
	hello() {
		print("hello");
	}
}
```

main.dart：

```dart
import 'lib.dart' deferred as lib;

void main(List<String> args) {
	print("main");
	hello();
}

void hello() async {
	await lib.loadLibrary();
	new lib.Hello().hello();
}
```

# 包

包目录结构如下：

* lib/：源代码目录。
	* src/：私有源代码目录。
* pubspec.yaml：配置文件。

flutter包的material.dart：

```dart
export 'src/material/app.dart';
```

main.dart：

```dart
import 'package:flutter/material.dart';

void main() {
	runApp(const MyApp());
}

class MyApp extends StatelessWidget {
	const MyApp({super.key});
	
	@override
	Widget build(BuildContext context) {
		return const MaterialApp(
			title: 'Flutter Demo'
		);
	}
}
```
