本文更新于2022-06-16。

**说明：下文中，大写为自定义变量（个别大写的类型除外），根据实际情况填写。使用`<>`引起表示内容可选，`|`表示使用左侧或右侧内容，`...`表示重复之前内容。**

本文只说明简要的使用，更详细的使用可参阅MDN文档：[https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)

[TOC]

全局对象以字典序进行排序。全局对象的属性先以此方式进行排序：静态属性（如`Number.MAX_VALUE`）、实例属性（如`ARRAY.length`）、静态方法（如`Date.now`）、prototype方法（如`Array.prototype.concat`）；同一类别属性再以字典序进行排序。

本文阐述的API标准为：ECMAScript 5。

以下接口已弃用：

* **escape**
* **unescape**

# arguments

arguments所在函数的实参，为类数组对象，只定义在函数体内。

## arguments.callee

arguments所在的函数。

## arguments.length

arguments所在函数的实参个数。

# Array

数组类型。

```js
var ARRAY = new Array();
var ARRAY = new Array(LENGTH);
var ARRAY = new Array(ELEMENT <, ...>);
```

## ARRAY.length

数组元素个数。可写属性。

## Array.prototype.concat

拼接数组。

```js
var NEW_ARRAY = ARRAY.concat(ELEMENT <, ...>);
```

## Array.prototype.every

判断是否所有元素都满足断言。

```js
var OK = ARRAY.every(
	function(ELEMENT, I, ARRAY) {
		return true;
	}
	<, THIS>
);
```

THIS为调用断言函数时this的指向，下同。

## Array.prototype.filter

使用通过断言的元素生产新数组。

```js
var NEW_ARRAY = ARRAY.filter(
	function(ELEMENT, I, ARRAY) {
		return true;
	}
	<, THIS>
);
```

## Array.prototype.forEach

为每个元素调用一个函数。

```js
ARRAY.forEach(
	function(ELEMENT, I, ARRAY) {
	}
	<, THIS>
);
```

## Array.prototype.indexOf

正向查找元素。使用===判断相等，如不存在，则返回-1。

```js
var INDEX = ARRAY.indexOf(VALUE <, START_INDEX>);
```

## Array.prototype.join

将数组拼接为字符串。

```js
var STR = ARRAY.join(<SEPARATOR>);
```

SEPARATOR缺省为","。

## Array.prototype.lastIndexOf

反向查找元素。使用===判断相等，如不存在，则返回-1。

```js
var INDEX = ARRAY.lastIndexOf(VALUE, <, START_INDEX>);
```

## Array.prototype.map

计算出新数组。

```js
var NEW_ARRAY = ARRAY.map({
	function(ELEMENT, I, ARRAY) {
		return VALUE;
	}
	<, THIS>
});
```

## Array.prototype.pop

移除并返回最后一个元素。

```js
var ELEMENT = ARRAY.pop();
```

## Array.prototype.push

向数组尾部追加元素。

```js
var LENGTH = ARRAY.push(ELEMENT <, ...>);
```

## Array.prototype.reduce

从数组正向计算出一个值。

```js
var VALUE = ARRAY.reduce(
	function(RESULT1, ELEMENT, I, ARRAY) {
		return RESULT2;
	}
	<, INITIAL>
);
```

如指定INITIAL，则函数参数第一次调用时传入INITIAL和数组第一个元素；否则，函数参数第一次调用时传入第一和第二个元素。

## Array.prototype.reduceRight

从数组反向计算出一个值。

```js
var VALUE = ARRAY.reduceRight(
	function(RESULT1, ELEMENT, I, ARRAY) {
		return RESULT2;
	}
	<, INITIAL>
);
```

如指定INITIAL，则函数参数第一次调用时传入INITIAL和数组最后一个元素；否则，函数参数第一次调用时传入最后一个和倒数第二个元素。

## Array.prototype.reverse

颠倒数组元素。

```js
ARRAY.reverse();
```

## Array.prototype.shift

移除并返回第一个元素。

```js
var ELEMENT = ARRAY.shift();
```

## Array.prototype.slice

使用数组的一部分生成新数组。

```js
var NEW_ARRAY = ARRAY.slice(<START_INDEX <, END_INDEX>>);
```

新数组对应ARRAY的区间为[START_INDEX, END_INDEX)。START_INDEX和END_INDEX均可为负数，表示从尾部数起，-1为最后一个元素。

## Array.prototype.some

判断是否有元素满足断言。

```js
var OK = ARRAY.some(
	function(ELEMENT, I, ARRAY) {
		return true;
	}
	<, THIS>
);
```

## Array.prototype.sort

对数组进行排序。

```js
var ARRAY = ARRAY.sort(
	<function(ELEMENT1, ELEMENT2) {
		return INT;
	}>
);
```

如不使用函数参数，则将数组元素转换成字符串后的值按字典序进行排序。如函数参数返回值小于0，表示ELEMENT1<ELEMENT2；等于0，表示ELEMENT1==ELEMENT2；大于0，表示ELEMENT1>ELEMENT2。

## Array.prototype.splice

可用于插入、删除或替换数组元素。

```js
var DELETE_ARRAY = ARRAY.splice(
	START_INDEX,
	DELETE_COUNT
	<, ELEMENT <, ...>>
);
```

先删除START_INDEX（含）起的DELETE_COUNT个元素，再在START_INDEX处插入指定的元素，并返回被删除的元素数组。

## Array.prototype.toLocaleString

转换成本地化字符串。

```js
var STR = ARRAY.toLocaleString();
```

## Array.prototype.toString

转换成字符串。

```js
var STR = ARRAY.toString();
```

返回值与`ARRAY.join()`一样。

## Array.prototype.unshift

往数组头部添加元素。

```js
var LENGTH = ARRAY.unshift(ELEMENT <, ...>);
```

# Boolean

布尔类型。

```js
var BOOLEAN = new Boolean(VALUE);
var BOOLEAN = Boolean(VALUE);
```

## Boolean.prototype.toString

转换成字符串。

```js
var STR = BOOLEAN.toString();
```

返回"true"或"false"。

## Boolean.prototype.valueOf

返回原始布尔值。

```js
var BOOL = BOOLEAN.valueOf();
```

# Date

日期时间类型。

```js
var DATE = new Date(MILLISECONDS);
var DATE = new Date();
var DATE = new Date(STR);
var DATE = new Date(YEAR, MONTH <, DATE_IN_MONTH <, HOUR <, MINUTE <, SECOND <, MILLISECOND>>>>>);
var STR = Date();
```

当传入MILLISECONDS时，为UTC时间从1970年1月1日零点经过指定毫秒数后对应的本地时间。其他调用，均为参数指定的本地时间。

STR最完整的格式为：2006-01-02T15:04:05.000Z。

MONTH为0至11，DATEINMONTH为1至31，HOUR为0至23，MINUTE为0至59，SECOND为0至59，MILLISECOND为0至999。

以下接口已弃用：

* **Date.prototype.getYear**
* **Date.prototype.setYear**
* **Date.prototype.toGMTString**

## Date.now

返回UTC从1970年1月1日零点到现在UTC时间经过的毫秒数。

```js
var MILLISECONDS = Date.now();
```

## Date.parse

从字符串解析日期时间。

```js
var DATE = Date.parse(STR);
```

STRING最完整的格式为：2006-01-02T15:04:05.000Z。

## Date.UTC

返回参数指定的UTC时间从1970年1月1日零点到现在UTC时间经过的毫秒数。

```js
var MILLISECONDS = Date.UTC(YEAR, MONTH <, DATE_IN_MONTH <, HOUR <, MINUTE <, SECOND <, MILLISECOND>>>>>)
```

## Date.prototype.getDate

返回本地时间的日。

```js
var DATE_IN_MONTH = DATE.getDate();
```
## Date.prototype.getDay

返回本地时间的星期。

```js
var DAY = DATE.getDay();
```

返回值为0（周日）至6（周六）。

## Date.prototype.getFullYear

返回本地时间的年。

```js
var YEAR = DATE.getFullYear();
```

## Date.prototype.getHours

返回本地时间的时。

```js
var HOUR = DATE.getHours();
```

## Date.prototype.getMilliseconds

返回本地时间的毫秒。

```js
VAR MILLISECOND = DATE.getMilliseconds();
```

## Date.prototype.getMinutes

返回本地时间的分。

```js
var MINUTE = DATE.getMinutes();
```

## Date.prototype.getMonth

返回本地时间的月。

```js
var MONTH = DATE.getMonth();
```

## Date.prototype.getSeconds

返回本地时间的秒。

```js
var SECOND = DATE.getSeconds();
```

## Date.prototype.getTime

返回对应的UTC时间从UTC时间1970年1月1日零点经过的毫秒数。

```js
var MILLISECONDS = DATE.getTime();
```

## Date.prototype.getTimezoneOffset

返回本地时区与UTC时区的时差，以分钟表示。

```js
var MINUTES = DATE.getTimezoneOffset();
```

## Date.prototype.getUTCDate

返回UTC时间的日。

```js
var DATE_IN_MONTH = DATE.getUTCDate();
```
## Date.prototype.getUTCDay

返回UTC时间的星期。

```js
var DAY = DATE.getUTCDay();
```

返回值为0（周日）至6（周六）。

## Date.prototype.getUTCFullYear

返回UTC时间的年。

```js
var YEAR = DATE.getUTCFullYear();
```

## Date.prototype.getUTCHours

返回UTC时间的时。

```js
var HOUR = DATE.getUTCHours();
```

## Date.prototype.getUTCMilliseconds

返回UTC时间的毫秒。

```js
VAR MILLISECOND = DATE.getUTCMilliseconds();
```

## Date.prototype.getUTCMinutes

返回UTC时间的分。

```js
var MINUTE = DATE.getUTCMinutes();
```

## Date.prototype.getUTCMonth

返回UTC时间的月。

```js
var MONTH = DATE.getUTCMonth();
```

## Date.prototype.getUTCSeconds

返回UTC时间的秒。

```js
var SECOND = DATE.getUTCSeconds();
```

## Date.prtotype.setDate

设置本地时间的日。

```js
var MILLISECONDS = DATE.setDate(DATE_IN_MONTH);
```

返回设置后的UTC时间从UTC时间1970年1月1日零点经过的毫秒数。下同。

## Date.prototype.setFullYear

设置本地时间的年、月、日。

```js
var MILLISECONDS = DATE.setFullYear(YEAR <, MONTH <, DATE_IN_MONTH>>);
```

## Date.prototype.setHours

设置本地时间的时、分、秒、毫秒。

```js
var MILLISECONDS = DATE.setHours(HOUR <, MINUTE <, SECOND <, MILLISECOND>>>);
```

## Date.prototype.setMilliseconds

设置本地时间的毫秒。

```js
var MILLISECONDS = DATE.setMilliseconds(MILLISECOND);
```

## Date.prototype.setMinutes

设置本地时间的分、秒、毫秒。

```js
var MILLISECONDS = DATE.setMinutes(MINUTE <, SECOND <, MILLISECOND>>);
```

## Date.prototype.setMonth

设置本地时间的月、日。

```js
var MILLISECONDS = DATE.setMonth(MONTH <, DATE_IN_MONTH>);
```

## Date.prototype.setSeconds

设置本地时间的秒、毫秒。

```js
var MILLISECONDS = DATE.setSeconds(SECOND <, MILLISECOND>);
```

## Date.prototype.setTime

设置时间为UTC时间从1970年1月1日零点经过指定的毫秒数。

```js
var MILLISECONDS = DATE.setTime(MILLISECONDS);
```

## Date.prtotype.setUTCDate

设置UTC时间的日。

```js
var MILLISECONDS = DATE.setUTCDate(DATE_IN_MONTH);
```

返回设置后的UTC时间从UTC时间1970年1月1日零点经过的毫秒数。下同。

## Date.prototype.setUTCFullYear

设置UTC时间的年、月、日。

```js
var MILLISECONDS = DATE.setUTCFullYear(YEAR <, MONTH <, DATE_IN_MONTH>>);
```

## Date.prototype.setUTCHours

设置UTC时间的时、分、秒、毫秒。

```js
var MILLISECONDS = DATE.setUTCHours(HOUR <, MINUTE <, SECOND <, MILLISECOND>>>);
```

## Date.prototype.setUTCMilliseconds

设置UTC时间的毫秒。

```js
var MILLISECONDS = DATE.setUTCMilliseconds(MILLISECOND);
```

## Date.prototype.setUTCMinutes

设置UTC时间的分、秒、毫秒。

```js
var MILLISECONDS = DATE.setUTCMinutes(MINUTE <, SECOND <, MILLISECOND>>);
```

## Date.prototype.setUTCMonth

设置UTC时间的月、日。

```js
var MILLISECONDS = DATE.setUTCMonth(MONTH <, DATE_IN_MONTH>);
```

## Date.prototype.setUTCSeconds

设置UTC时间的秒、毫秒。

```js
var MILLISECONDS = DATE.setUTCSeconds(SECOND <, MILLISECOND>);
```

## Date.prototype.toDateString

返回本地时间的日期字符串，与实现有关，如：Tue Jul 07 2020。

```js
var STR = DATE.toDateString();
```

## Date.prototype.toISOString

返回UTC时间的日期时间字符串，使用ISO-8601标准，如：2020-07-07T15:56:14.306Z。

```js
var STR = DATE.toISOString();
```

## Date.prototype.toJSON

返回UTC时间的日期时间字符串，使用ISO-8601标准，如：2020-07-07T15:56:14.306Z。

```js
var STR = DATE.toJSON(<KEY>);
```

参数KEY会被忽略。

## Date.prototype.toLocaleDateString

返回本地时间本地习惯的日期字符串，与实现有关，如：2020/7/7。

```js
var STR = DATE.toLocaleDateString();
```

## Date.prototype.toLocaleString

返回本地时间本地习惯的日期时间字符串，与实现有关，如：2020/7/7 下午11:56:14。

```js
var STR = DATE.toLocaleString();
```

## Date.prototype.toLocaleTimeString

返回本地时间本地习惯的时间字符串，与实现有关，如：下午11:56:14。

```js
var STR = DATE.toLocaleTimeString();
```

## Date.prototype.toString

返回本地时间的日期时间字符串，与实现有关，如：Tue Jul 07 2020 23:56:14 GMT+0800 (中国标准时间)。

```js
var STR = DATE.toString();
```

## Date.prototype.toTimeString

返回本地时间的时间字符串，与实现有关，如：23:56:14 GMT+0800 (中国标准时间)。

```js
var STR = DATE.toTimeString();
```

## Date.prototype.toUTCString

返回UTC时间的日期时间字符串，与实现有关，如：Tue, 07 Jul 2020 15:56:14 GMT。

```js
var STR = DATE.toUTCString();
```

## Date.prototype.valueOf

返回对应的UTC时间从UTC时间1970年1月1日零点经过的毫秒数。

```js
var MILLISECONDS = DATE.valueOf();
```

# decodeURI

解码URI，将%开头的16进制转义序列解码为其代表的字符。encodeURI的逆操作。

```js
var URI_DECODED = decodeURI(URI);
```

# decodeURIComponent

解码URI的组件，将%开头的16进制转义序列解码为其代表的字符。encodeURIComponent的逆操作。

```js
var COMPONENT_DECODED = decodeURIComponent(COMPONENT);
```

# encodeURI

编码URI。将字母、数字、-_.!~*'()和;/?:@&=+$,#以外的字符编码为%开头的16进制转义序列，不会对那些用于分隔URI组件的字符进行转义。

```js
var URI_ENCODED = encodeURI(URI);
```

# encodeURIComponent

编码URI组件。将字母、数字和-_.!~*'()以外的字符编码为%开头的16进制转义序列，会对那些用于分隔URI组件的字符进行转义。

```js
var COMPONENT_ENCODED = encodeURIComponent(COMPONENT);
```

# Error

错误类型。

```js
var ERROR = new Error(<MESSAGE>);
```

## ERROR.message

错误消息。

## ERROR.name

创建错误实例的构造函数名字。

## Error.prototype.toString

转换成字符串。

```js
var STR = ERROR.toString();
```

# eval

执行JavaScript代码，并返回执行代码的求值结果。

```js
var RETURN = eval(STR);
```

# EvalError

执行代码错误的错误类型。

```
var EVALERROR = new EvalError(<MESSAGE>);
```

## EVALERROR.message

错误消息。

## EVALERROR.name

创建错误实例的构造函数名字，即：EvalError。

# Function

函数类型。

```js
var FUNCTION = new Function(STR_PARAM_NAME <, ...> STR_BODY);
```

以下接口已弃用：

* **FUNCTION.arguments**
* **FUNCTION.caller**

## FUNCTION.length

形参的个数。

## FUNCTION.prototype

原型对象，当FUNCTION作为构造函数使用时，创建的对象都会继承原型对象的属性和方法。

## Function.prototype.apply

将函数作为一个对象的方法调用。

```js
var RETURN = FUNCTION.apply(THIS <, PARAM_ARRAY>);
```

## Function.prototype.bind

将函数作为一个对象的方法绑定生成一个新函数。

```js
var FUNCTION_BOUND = FUNCTION.bind(THIS <, PARAM <, ...>>);
```

## Function.prototype.call

将函数作为一个对象的方法调用。

```js
var RETURN = FUNCTION.call(THIS <, PARAM <, ...>>);
```

## Function.prototype.toString

转换成字符串。

```js
var STR = FUNCTION.toString();
```

# Infinity

无穷大。

# isFinite

是否是有限数。

```js
var OK = isFinite(NUM);
```

# isNaN

是否非数值。

```js
var OK = isNaN(VALUE);
```

# JSON

JSON序列化与反序列化对象。

## JSON.parse

将字符串反序列化为原始值。

```js
var DATA = JSON.parse(
	STR
	<, function(KEY_OR_INDEX, VALUE) {
		return undefined|RESULT_VALUE;
	}>
);
```

函数参数的返回值如为undefined，则去除属性KEY_OR_INDEX。

## JSON.stringify

将原始值序列化为字符串。

```js
var STR = JSON.stringify(
	DATA
	<, KEY_ARRAYAY|function(KEY_OR_INDEX, VALUE) {
		return undefined|RESULT_STR;
	}
	<, INDENT|N>
	>
);
```

函数参数的返回值如为undefined，则不对属性KEY_OR_INDEX进行序列化。

每行缩进只取INDENT前10个字符，或当1<=N<=10时使用N个空格作为缩进。

# Math

数学对象。

## Math.E

e，近似值为2.71828。

## Math.LN10

ln(10)，近似值为2.30259。

## Math.LN2

ln(2)，近似值为0.69315。

## Math.LOG10E

lg(e)，近似值为0.43429。

## Math.LOG2E

log2(e)，近似值为1.44270。

## Math.PI

π，近似值为3.14159。

## Math.SQRT1_2

1/√2，近似值为0.70711。

## Math.SQRT2

√2，近似值为1.41421。

## Math.abs

绝对值。

```js
var RESULT = Math.abs(VALUE);
```

## Math.acos

反余弦值，结果为弧度。

```js
var RESULT = Math.acos(VALUE);
```

## Math.asin

反正弦值，结果为弧度。

```js
var RESULT = Math.asin(VALUE);
```

## Math.atan

反正切值，结果为弧度。

```js
var RESULT = Math.atan(VALUE);
```

## Math.atan2

从x轴到指定点的弧度。

```js
var RESULT = Math.atan2(Y, X);
```

等同于`Math.atan(Y/X)`。

## Math.ceil

向上取整。

```js
var RESULT = Math.ceil(VALUE);
```

## Math.cos

余弦值，参数为弧度。

```js
var RESULT = Math.cos(VALUE);
```

## Math.exp

e的VALUE次方。

```js
var RESULT = Math.exp(VALUE);
```

## Math.floor

向下取整。

```js
var RESULT = Math.floor(VALUE);
```

## Math.log

ln(VALUE)。

```js
var RESULT = Math.log(VALUE);
```

## Math.max

最大值。

```js
var RESULT = Math.max(VALUE <, ...>);
```

## Math.min

最小值。

```js
var RESULT = Math.min(VALUE <, ...>);
```

## Math.pow

X的Y次方。

```js
var RESULT = Math.pow(X, Y);
```

## Math.random

伪随机数值，区间范围为[0.0, 1.0)。

```js
var RESULT = Math.random();
```

## Math.round

四舍五入值。

```js
var RESULT = Math.round(VALUE);
```

## Math.sin

正弦值，参数为弧度。

```js
var RESULT = Math.sin(VALUE);
```

## Math.sqrt

平方根值。

```js
var RESULT = Math.sqrt(VALUE);
```

## Math.tan

正切值，参数为弧度。

```js
var RESULT = Math.tan(VALUE);
```

# NaN

非数值。

# Number

数值类型。

```js
var NUMBER = new Number(NUM);
var NUMBER = Number(NUM);
```

## Number.MAX_VALUE

最大的数值，近似值为1.79e+308。

## Number.MIN_VALUE

最小的数值，近似值为5e-324。

## Number.NaN

非数值，等同于`NaN`。

## Number.NEGATIVE_INFINITY

负无穷大，等同于`-Infinity`。

## Number.POSITIVE_INFINITY

正无穷大，等同于`Infinity`。

## Number.prototype.toExponential

返回指数记数法表示的字符串。

```js
var STR = NUMBER.toExponential(<N_FRACTION_DIGITS>);
```

## Number.prototype.toFixed

返回定点记数法表示的字符串。

```js
var STR = NUMBER.toFixed(<N_FRACTION_DIGITS>);
```

## Number.prototype.toLocaleString

返回本地格式表示的字符串。

```js
var STR = NUMBER.toLocaleString();
```

## Number.prototype.toPrecision

返回有效数字为指定精度的字符串。

```js
var STR = NUMBER.toPrecision(<N_PRECISION>);
```

## Number.prototype.toString

返回指定进制表示的字符串。

```js
var STR = NUMBER.toString(<RADIX>);
```

## Number.prototype.valueOf

返回原始数字值。

```js
var NUM = NUMBER.valueOf();
```

# Object

对象类型。

```js
var OBJECT = new Object(<VALUE>);
```

## OBJECT.constructor

对象的构造函数。

## Object.create

使用指定的原型和属性创建对象。

```js
var OBJECT = Object.create(
	PROTO
	<, {
		KEY: {
			<value: VALUE>
			<, writable: BOOL>
			<, enumerable: BOOL>
			<, configurable: BOOL>
			<, get function() {return VALUE;}>
			<, set function(VALUE) {}>
		}
		<, ...>
	}>
);
```

属性描述对象各字段的含义如下（下同）：

* value：属性值。数据属性有此字段。
* writable：属性是否是可写的。数据属性有此字段。
* enumerable：属性是否是可枚举的。数据属性和存取器属性都有此字段。
* configurable：属性是否是可配置的，即是否可对属性的描述对象进行配置，以及属性是否可删除。数据属性和存取器属性都有此字段。
* get：属性存取器getter。存取器属性有此字段。
* set：属性存取其setter。存取器属性有此字段。

## Object.defineProperties

定义对象的多个属性。

```js
var OBJECT = Object.defineProperties(
	OBJECT
	<, {
		KEY: {
			<value: VALUE>
			<, writable: BOOL>
			<, enumerable: BOOL>
			<, configurable: BOOL>
			<, get function() {return VALUE;}>
			<, set function(VALUE) {}>
		}
		<, ...>
	}>
);
```

## Object.defineProperty

定义对象的一个属性。

```js
var OBJECT = Object.defineProperty(
	OBJECT
	, KEY
	<, {
		<value: VALUE>
		<, writable: BOOL>
		<, enumerable: BOOL>
		<, configurable: BOOL>
		<, get function() {return VALUE;}>
		<, set function(VALUE) {}>
	}>
);
```

## Object.freeze

冻结对象。即将对象设为封闭的（就像Object.seal那样），且所有非继承属性都设置为只读（writable为false），但不影响存取器setter属性和继承属性。这是永久不可逆操作。

```js
var OBJECT = Object.freeze(OBJECT);
```

## Object.getOwnPropertyDescriptor

返回一个非继承属性的描述对象。

```js
/*
{
	<value: VALUE>
	<, writable: BOOL>
	, enumerable: BOOL
	, configurable: BOOL
	<, get function() {return VALUE;}>
	<, set function(VALUE) {}>
}
*/
var DESCRIPTOR = Object.getOwnPropertyDescriptor(OBJECT, KEY);
```

## Object.getOwnPropertyNames

返回对象所有非继承属性的名字，包括不可枚举的属性。

```js
var KEY_ARRAY = Object.getOwnPropertyNames(OBJECT);
```

## Object.getPrototypeOf

返回对象的原型。

```js
var PROTO = Object.getPrototypeOf(OBJECT);
```

## Object.isExtensible

检查对象是否是可扩展的。可扩展的即可添加新的非继承属性。

```js
var OK = Object.isExtensible(OBJECT);
```

## Object.isFrozen

检查对象是否是冻结的。冻结的即封闭的，且所有非继承属性都为只读（writable为false）。

```js
var OK = Object.isFrozen(OBJECT);
```

## Object.isSealed

检查对象是否是封闭的。封闭的即不可添加新的非继承属性（不可扩展的），且已有的非继承属性不可配置（configurable为false）。

```js
var OK = Object.isSealed(OBJECT);
```

## Object.keys

返回对象所有非继承可枚举的属性名。

```js
var KEY_ARRAY = Object.keys(OBJECT);
```

## Object.preventExtensions

将对象设置为不可扩展的。即不可以添加新的非继承属性，但不影响继承属性。这是永久不可逆操作。

```js
var OBJECT = Object.preventExtensions(OBJECT);
```

## Object.seal

封闭对象。即不可添加新的非继承属性（不可扩展的），且已有的非继承属性设为不可配置（configurable为false），但不影响继承属性。这是永久不可逆操作。

```js
var OBJECT = Object.seal(OBJECT);
```

## Object.prototype.hasOwnProperty

检查属性是否是非继承的。

```js
var OK = OBJECT.hasOwnProperty(KEY);
```

## Object.prototype.isPrototypeOf

检查当前对象是否是另一对象的原型。

```js
var OK = OBJECT.isPrototypeOf(OTHER_OBJECT);
```

## Object.prototype.propertyIsEnumerable

检查属性是否是非继承可枚举的。

```js
var OK = OBJECT.propertyIsEnumerable(KEY);
```

## Object.prototype.toLocaleString

返回本地格式表示的字符串。

```js
var STR = OBJECT.toLocaleString();
```

## Object.prototype.toString

转换成字符串。

```js
var STR = OBJECT.toString();
```

## Object.prototype.valueOf

返回对象原始值。如果没有，则返回对象本身。

```js
var VALUE = OBJECT.valueOf();
```

# parseFloat

将字符串解析为浮点数。当遇到不是组成浮点数的有效字符时，解析将终止并返回解析值，可能返回`NaN`。

```js
var FLOAT = parseFloat(STR);
```

# parseInt

将字符串解析为整数。当遇到不是组成整数的有效字符时，解析将终止并返回解析值，可能返回`NaN`。

```js
var INT = parseInt(STRING <, RADIX>);
```

STRING可以用0x或0X的格式表示16进制。

# RangeError

数值超出合法范围的错误类型，包括数组下标越界。

```js
var RANGEERROR = new RangeError(<MESSAGE>);
```

## RANGEERROR.message

错误消息。

## RANGEERROR.name

创建错误实例的构造函数名字，即：RangeError。

# ReferenceError

引用不存在的变量的错误类型。

```js
var REFERENCEERROR = new ReferenceError(<MESSAGE>);
```

## REFERENCEERROR.message

错误消息。

## REFERENCEERROR.name

创建错误实例的构造函数名字，即：ReferenceError。

# RegExp

正则表达式类型。

```js
var REGEXP = new RegExp(PATTERN <, ATTRIBUTES>);
```

返回值对应的字面量为`/PATTERN/ATTRIBUTES`。ATTRIBUTES可为g（全局匹配，即找到所有匹配，而不是找到第一个就停止）、i（忽略大小写匹配）、m（多行匹配，即^不只匹配字符串开头也匹配行首，$不只匹配字符串结尾也匹配行尾）的任意组合。

## REGEXP.global

是否是全局匹配，即是否有g修饰符。

## REGEXP.ignoreCase

是否忽略大小写匹配，即是否有i修饰符。

## REGEXP.lastIndex

字符串下一次匹配时的开始下标。

用于全局匹配。当REGEXP.exec或REGEXP.test没有找到匹配项时，会自动将该值设为0；当找到匹配项时，会将该值设为匹配项之后第一个字符的下标，以允许重复调用来遍历所有匹配项。如找到匹配项后需重新搜索一个新的字符串是否匹配，需显式将该值设为0。

## REGEXP.multiline

是否是多行匹配，即是否有m修饰符。

## REGEXP.source

对应的源文本，即PATTERN。

## RegExp.prototype.exec

通用的模式匹配。

```js
/*
[
	MATCHED_STR
	<, MATCHED_STR_OF_GROUP <, ...>>,
	index: BEGIN_INDEX_OF_MATCHED_STR,
	input: STR
]
*/
var ARRAY = REGEXP.exec(STR);
```

如找到匹配项，则返回一个数组：第一个元素MATCHED_STR是STR中与正则表达式匹配的字符串，其后的元素MATCHED_STR_OF_GROUP是MATCHED_STR与正则表达式各个分组（使用“()”引起）匹配的字符串，index为MATCHED_STR第一个字符在STR中的下标，input为REGEXP.exec的参数即STR。如找不到匹配项，则返回null。

## RegExp.prototype.test

测试字符串是否匹配。

```js
var OK = REGEXP.test(STR);
```

## RegExp.prototype.toString

转换成字符串。

```js
var STR = REGEXP.toString();
```

返回的字符串跟字面量的形式/PATTERN/ATTRIBUTES一样，可能带有转义字符。

# String

字符串类型。

```js
var STRING = new String(STR);
```

以下接口已弃用：

* **String.prototype.substr**

## STRING.length

字符串字符个数。只读属性。

## String.fromCharCode

使用Unicode编码创建字符串。

```js
var STR = String.fromCharCode(CODE <, ...>);
```

## String.prototype.charAt

返回第N个字符。如下标越界，则返回空字符串。

```js
var STR = STRING.charAt(N);
```

## String.prototype.charCodeAt

返回第N个字符的Unicode编码，为16位的整数。如下标越界，则返回NaN。

```js
var CODE = STRING.charCodeAt(N);
```

## String.prototype.concat

拼接字符串。

```js
var NEW_STR = STRING.concat(STR <, ...>);
```

## String.prototype.indexOf

正向查找字符串。返回子字符串第一个字符在原字符串中的下标，如不存在，则返回-1。

```js
var INDEX = STRING.indexOf(SUB_STR <, START_INDEX>);
```

## String.prototype.lastIndexOf

反向查找字符串。返回子字符串第一个字符在原字符串中的下标，如不存在，则返回-1。

```js
var INDEX = STRING.lastIndexOf(SUB_STR <, START_INDEX>);
```

## String.prototype.localeCompare

使用本地规则比较字符串。该函数利用操作系统提供的排序。

```js
var RESULT = STRING.localeCompare(OTHER_STR);
```

如返回值小于0，表示STRING<OTHER_STR；等于0，表示STRING==OTHER_STR或无法区分；大于0，表示STRING>OTHER_STR。

## String.prototype.match

搜索匹配的子字符串。

```js
// 当REGEXP为全局匹配时：
/*
[MATCHED_STR <, ...>]
*/
// 当REGEXP不为全局匹配时：
/*
[
	MATCHED_STR
	<, MATCHED_STR_OF_GROUP <, ...>>,
	index: BEGIN_INDEX_OF_MATCHED_STR,
	input: STRING
]
*/
var ARRAY = STRING.match(REGEXP);
```

当REGEXP为全局匹配时，如找到匹配项，则返回一个数组：各个元素MATCHED_STR是STRING中与正则表达式匹配的字符串。如找不到匹配项，则返回null。

当REGEXP不为全局匹配时，如找到匹配项，则返回一个数组：第一个元素MATCHED_STR是STRING中与正则表达式匹配的字符串，其后的元素MATCHED_STR_OF_GROUP是MATCHED_STR与正则表达式各个分组（使用“()”引起）匹配的字符串，index为MATCHED_STR第一个字符在STRING中的下标，input即STRING。如找不到匹配项，则返回null。

## String.prototype.replace

替换匹配的子字符串。

```js
var STR_REPLACED = STRING.replace(
	STR|REGEXP,
	REPLACEMENT_STR|function(MATCHED_STR <, MATCHED_STR_OF_GROUP <, ...>>, BEGIN_INDEX_OF_MATCHED_STR, STRING) {
		return REPLACEMENT_STR;
	}
);
```

当REGEXP为全局匹配时，替换所有匹配的子字符串。否则，只替换第一个匹配的子字符串。

REPLACEMENT_STR中的“$”有特殊含义：

* $N：匹配的子字符串中与正则表达式第N个分组（使用“()”引起）匹配的字符串。
* $&：匹配的子字符串。
* $`：匹配的子字符串左边的字符串。
* $'：匹配的子字符串右边的字符串。
* $$：$符号自身。

函数参数的参数中，第一个参数MATCHED_STR是STRING中与正则表达式匹配的字符串，其后的参数MATCHED_STR_OF_GROUP是MATCHED_STR与正则表达式各个分组（使用“()”引起）匹配的字符串，BEGIN_INDEX_OF_MATCHED_STR为MATCHED_STR第一个字符在STRING中的下标，STRING即String对象自身。

## String.prototype.search

查找匹配的子字符串。

```js
var INDEX = STRING.search(REGEXP);
```

返回第一个匹配的子字符串的第一个字符在STRING中的下标，如不存在，则返回-1。总是从头开始搜索，不进行全局匹配，即忽略g修饰符和lastIndex属性。

## String.prototype.slice

使用字符串的一部分生成新字符串。

```js
var NEW_STR = STRING.slice(START_INDEX <, END_INDEX>);
```

新字符串对应STRING的区间为[START_INDEX, END_INDEX)。START_INDEX和END_INDEX均可为负数，表示从尾部数起，-1为最后一个元素。

## String.prototype.split

将字符串切割成数组。

```js
var ARRAY = STRING.split(<DELIMITER|REGEXP <, LIMIT>>);
```

如果不指定分隔规则，则返回只包含字符串自身一个元素的数组。

如果分隔规则为空字符串，则返回的数组将字符串每个字符作为一个元素。

如果分隔规则匹配字符串开头的内容，则第一个元素是空字符串；如果分隔规则匹配字符串结尾的内容，则最后一个元素是空字符串。（分隔规则为空字符串的时候除外。）

如果分隔规则为带有分组（使用“()”引起）的正则表达式，则匹配分组的子字符串也会包含在数组中。

## String.prototype.substring

返回子字符串。

```js
var NEW_STR = STRING.substring(START_INDEX <, END_INDEX>);
```

新字符串对应STRING的区间为[START_INDEX, END_INDEX)。START_INDEX和END_INDEX均不可为负数。

## String.prototype.toLocaleLowerCase

转换成本地格式小写的字符串。

```js
var NEW_STR = STRING.toLocaleLowerCase();
```

## String.prototype.toLocaleUpperCase

转换成本地格式大写的字符串。

```js
var NEW_STR = STRING.toLocaleUpperCase();
```

## String.prototype.toLowerCase

转换成小写字符串。

```js
var NEW_STR = STRING.toLowerCase();
```

## String.prototype.toString

返回原始字符串值。

```js
var STR = STRING.toString();
```

## String.prototype.toUpperCase

转换成大写字符串。

```js
var NEW_STR = STRING.toUpperCase();
```

## String.prototype.trim

去除开头和结尾的空白字符。

```js
var NEW_STR = STRING.trim();
```

## String.prototype.valueOf

返回原始字符串值。

```js
var STR = STRING.valueOf();
```

# SyntaxError

语法错误的错误类型。

```js
var SYNTAXERROR = new SyntaxError(<MESSAGE>);
```

## SYNTAXERROR.message

错误消息。

## SYNTAXERROR.name

创建错误实例的构造函数名字，即：SyntaxError。

# TypeError

值类型错误的错误类型。

```js
var TYPEERROR = new TypeError(<MESSAGE>);
```

## TypeError.message

错误消息。

## TypeError.name

创建错误实例的构造函数名字，即：TypeError。

# undefined

未定义值。

undefined不是常量，可以设置为其他值，但必须非常谨慎。

# URIError

URI编码或解码错误的错误类型。

```js
var URIERROR = new URIError(<MESSAGE>);
```

## URIError.message

错误消息。

## URIError.name

创建错误实例的构造函数名字，即：URIError。
