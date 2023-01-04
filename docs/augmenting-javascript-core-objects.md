# 扩充 JavaScript 核心对象

> 原文：<https://www.sitepoint.com/augmenting-javascript-core-objects/>

JavaScript 定义了几个对象，它们是其核心的一部分:`Array`、`Boolean`、`Date`、`Function`、`Math`、`Number`、`RegExp`和`String`。每个对象都扩展了`Object`，继承并定义了自己的属性和方法。我偶尔需要用新的属性和方法来扩充这些核心对象，并且已经用这些增强创建了一个库。在本文中，我展示了我对`Array`、`Boolean`、`Date`、`Math`、`Number`和`String`对象的各种增强。

我将新属性直接添加到核心对象中。例如，如果我需要一个常数`Math`来表示 3 的平方根，我会指定`Math.SQRT3 = 1.732050807;`。为了添加一个新方法，我首先确定该方法是与一个核心对象相关联(对象方法)还是与对象实例相关联(实例方法)。如果它与一个对象相关联，我就把它直接添加到这个对象上(例如，`Math.factorial = function(n) { ... }`)。如果它与对象实例相关联，我将它添加到对象的原型中(例如，`Number.prototype.abs = function() { ... }`)。

#### 方法和关键字`this`

在一个对象方法内，`this`指的是对象本身。在一个实例方法中，`this`指的是对象实例。比如在`" remove leading and trailing whitespace ".trim()`中，`this`指的是`String`的`trim()`方法中`String`对象的`" remove leading and trailing whitespace "`实例。

#### 名称冲突

由于名称冲突的可能性，您应该对扩充保持谨慎。例如，假设一个`factorial()`方法，它的实现不同于(并且可能比)你的`factorial()`方法，在将来被添加到`Math`中。你可能不想痛击新的`factorial()`方法。这个问题的解决方案是，在添加方法之前，总是测试核心对象是否存在同名的方法。以下代码片段展示了一个演示:

```
if (Math.factorial === undefined)
   Math.factorial = function(n)
                    {
                       // implementation
                    }
alert(Math.factorial(6));
```

当然，这个解决方案并不是万无一失的。可以添加一个参数列表不同于您的方法的参数列表的方法。为了绝对确保不会遇到任何问题，请在方法名前添加一个唯一的前缀。例如，您可以指定您的反向 Internet 域名。因为我的域名是`tutortutor.ca`，所以我会指定`Math.ca_tutortutor_factorial`。虽然这是一个繁琐的解决方案，但它应该会让那些担心名称冲突的人安心一些。

## 扩充数组

对象使得创建和操作数组成为可能。使这个对象更有用的两个方法是`equals()`，它比较两个数组是否相等，以及`fill()`，它将每个数组元素初始化为一个指定的值。

### 实施和测试`equals()`

下面的代码片段展示了一个`equals()`方法的实现，它简单地比较了两个数组——它不处理嵌套数组的情况:

```
Array.prototype.equals =
   function(array)
   {
      if (this === array)
         return true;

      if (array === null || array === undefined)
         return false;

      array = [].concat(array); // make sure this is an array

      if (this.length != array.length)
         return false;

      for (var i = 0; i < this.length; ++i) 
         if (this[i] !== array[i]) 
            return false;
      return true;
   };
```

用一个`array`参数调用`equals()`。如果当前数组和`array`引用同一个数组(`===`避免类型转换；类型必须相同才能相等)，此方法返回 true。

`equals()`下一步检查`array`中的`null`或`undefined`。当传递任一值时，此方法返回 false。假设`array`不包含任何值，`equals()`通过将`array`连接到一个空数组来确保它正在处理一个数组。

`equals()`比较数组长度，当这些长度不同时返回 false。然后它通过`!==`比较每个数组元素(以避免类型转换)，当不匹配时返回 false。此时，数组被认为是相等的和真实的返回。

一如既往，测试代码是必不可少的。以下测试案例运用了`equals()`方法，测试了各种可能性:

```
var array = [1, 2];
alert("array.equals(array): " + array.equals(array));

alert("['A', 'B'].equals(null): " + ['A', 'B'].equals(null));
alert("['A', 'B'].equals(undefined): " + ['A', 'B'].equals(undefined));

alert("[1].equals(4.5): " + [1].equals(4.5));

alert("[1].equals([1, 2]): " + [1].equals([1, 2]));

var array1 = [1, 2, 3, 'X', false];
var array2 = [1, 2, 3, 'X', false];
var array3 = [3, 2, 1, 'X', false];
alert("array1.equals(array2): " + array1.equals(array2));
alert("array1.equals(array3): " + array1.equals(array3));
```

当您运行这些测试用例时，您应该观察到以下输出(通过警告对话框):

```
array.equals(array): true
['A', 'B'].equals(null): false
['A', 'B'].equals(undefined): false
[1].equals(4.5): false
[1].equals([1, 2]): false
array1.equals(array2): true
array1.equals(array3): false
```

### 实施和测试`fill()`

下面的代码片段展示了一个`fill()`方法的实现，它用相同的值填充调用该方法的数组的所有元素:

```
Array.prototype.fill =
   function(item)
   {
      if (item === null || item === undefined)
         throw "illegal argument: " + item;

      var array = this;
      for (var i = 0; i < array.length; i++)
         array[i] = item;
      return array;
   };
```

用一个`item`参数调用`fill()`。如果`null`或`undefined`被传递，这个方法抛出一个异常来识别其中一个值。(您可能更喜欢用`null`或`undefined`填充数组。)否则，它用`item`填充整个数组并返回该数组。

我创建了以下测试用例来测试这个方法:

```
try
{
   var array = [0];
   array.fill(null);
}
catch (err)
{
   alert("cannot fill array with null");
}

try
{
   var array = [0];
   array.fill(undefined);
}
catch (err)
{
   alert("cannot fill array with undefined");
}

var array = [];
array.length = 10;
array.fill('X');
alert("array = " + array);

alert("[].fill(10) = " + [].fill(10));
```

当您运行这些测试用例时，您应该观察到以下输出:

```
cannot fill array with null
cannot fill array with undefined
array = X,X,X,X,X,X,X,X,X,X
[].fill(10) = 
```

## 扩充布尔运算

`Boolean`对象是布尔真/假值的对象包装器。我给这个对象添加了一个`parse()`方法，以便于将字符串解析为真/假值。以下代码片段展示了此方法:

```
Boolean.parse =
   function(s)
   {
      if (typeof s != "string" || s == "")
         return false;

      s = s.toLowerCase();
      if (s == "true" || s == "yes")
         return true;
      return false;
   };
```

对于任何不是字符串的参数、空字符串以及除`"true"`(不区分大小写)或`"yes"`(不区分大小写)之外的任何值，该方法都返回 false。对于这两种可能性，它返回 true。

以下测试案例练习了这种方法:

```
alert(Boolean.parse(null));
alert(Boolean.parse(undefined));
alert(Boolean.parse(4.5));
alert(Boolean.parse(""));
alert(Boolean.parse("yEs"));
alert(Boolean.parse("TRUE"));
alert(Boolean.parse("no"));
alert(Boolean.parse("false"));
```

当您运行这些测试用例时，您应该观察到以下输出:

```
false
false
false
false
true
true
false
false
```

## 增补日期

`Date`对象描述了基于时间值的单个时刻，该时间值是自 1970 年 1 月 1 日 UTC 以来的毫秒数。我已经向该对象添加了对象和实例`isLeap()`方法，以确定特定日期是否出现在闰年。

### 实现并测试一个`isLeap()`对象方法

以下代码片段展示了一个`isLeap()`对象方法的实现，该方法确定其`date`参数是否表示闰年:

```
Date.isLeap =
   function(date)
   {
      if (Object.prototype.toString.call(date) != '[object Date]')
         throw "illegal argument: " + date;

      var year = date.getFullYear();
      return (year % 400 == 0) || (year % 4 == 0 && year % 100 != 0);
   };
```

该方法不使用`date instanceof Date`表达式来确定`date`参数是否属于`Date`类型，而是使用更可靠的`Object.prototype.toString.call(date) != '[object Date]'`表达式来检查类型——当`date`来自另一个窗口时，`date instanceof Date`将返回 false。当检测到非`Date`参数时，会抛出一个异常来标识该参数。

在调用`Date`的`getFullYear()`方法从日期中提取四位数的年份后，`isLeap()`判断今年是否是闰年，返回 true 表示闰年。当一年能被 400 整除或能被 4 整除但不能被 100 整除时，它就是闰年。

以下测试案例练习了这种方法:

```
try
{
   alert(Date.isLeap(null));
}
catch (err)
{
   alert("null dates not supported.");
}

try
{
   alert(Date.isLeap(undefined));
}
catch (err)
{
   alert("undefined dates not supported.");
}

try
{
   alert(Date.isLeap("ABC"));
}
catch (err)
{
   alert("String dates not supported.");
}

var date = new Date();
alert(date + (Date.isLeap(date) ? " does " : " doesn't ") +
      "represent a leap year.");
```

当您运行这些测试用例时，您应该观察到类似如下的输出:

```
null dates not supported.
undefined dates not supported.
String dates not supported.
Wed Oct 23 2013 19:30:24 GMT-0500 (Central Standard Time) doesn't represent a leap year.
```

### 实现并测试一个`isLeap()`实例方法

下面的代码片段展示了一个`isLeap()`实例方法的实现，该方法确定当前的`Date`实例是否代表闰年:

```
Date.prototype.isLeap = 
   function()
   {
      var year = this.getFullYear();
      return (year % 400 == 0) || (year % 4 == 0 && year % 100 != 0);
   };
```

这个版本的`isLeap()`方法类似于它的前身，但是没有`date`参数。相反，它对当前的`Date`实例进行操作，该实例由`this`表示。

以下测试案例练习了这种方法:

```
date = new Date(2012, 0, 1);
alert(date + ((date.isLeap()) ? " does " : " doesn't ") + 
      "represent a leap year.");
date = new Date(2013, 0, 1);
alert(date + ((date.isLeap()) ? " does " : " doesn't ") + 
      "represent a leap year.");
```

当您运行这些测试用例时，您应该观察到类似如下的输出:

```
Sun Jan 01 2012 00:00:00 GMT-0600 (Central Daylight Time) does represent a leap year.
Tue Jan 01 2013 00:00:00 GMT-0600 (Central Daylight Time) doesn't represent a leap year.
```

## 增强数学

`Math`对象声明面向数学的对象属性和方法，不能被实例化。我给`Math`添加了一个`GOLDEN_RATIO`对象属性和`rnd()`、`toDegrees()`、`toRadians()`和`trunc()`对象方法。

#### 关于黄金比例

黄金分割比例是一个经常出现在几何中的数学常数。当两个量的比值等于它们之和与两个量中较大者的比值时，这两个量就处于黄金比例。换句话说，对于大于`b`的`a`，`a/b = (a+b)/a`。

### 实施和测试`GOLDEN_RATIO`和`rnd()`

以下代码片段展示了常量`GOLDEN_RATIO`和`rnd()`
方法的实现:

```
Math.GOLDEN_RATIO = 1.61803398874;

Math.rnd =
   function(limit)
   {
      if (typeof limit != "number")
         throw "illegal argument: " + limit;

      return Math.random() * limit | 0;
   };
```

在定义了`GOLDEN_RATIO`对象属性之后，这段代码定义了`rnd()`对象方法，该方法采用了一个`limit`参数。此参数必须是数字；否则，将引发异常。

`Math.random()`返回一个从 0.0 到(几乎)1.0 的分数值。乘以`limit`后，剩下一个小数。该分数通过截断来移除，截断是通过对结果进行按位“或”运算 0 来执行的。

按位 OR 使用一个`ToInt32`内部函数将其数值操作数转换为 32 位有符号整数。这个操作消除了数字的小数部分，比使用`Math.floor()`更有效，因为不需要方法调用。

以下测试用例测试了这些项目:

```
alert("Math.GOLDEN_RATIO: " + Math.GOLDEN_RATIO);

try
{
   alert("Math.rnd(null): " + Math.rnd(null));
}
catch (err)
{
   alert("null value not supported.");
}
alert("Math.rnd(10): " + Math.rnd(10));
```

当您运行这些测试用例时，您应该观察到类似如下的输出:

```
Math.GOLDEN_RATIO: 1.61803398874
null value not supported.
Math.rnd(10): 7
```

### 实施和测试`toDegrees()`、`toRadians()`和`trunc()`T3

下面的代码片段展示了`toDegrees()`、`toRadians()`和`trunc()`方法的实现:

```
Math.toDegrees = 
   function(radians)
   {
      if (typeof radians != "number")
         throw "illegal argument: " + radians;

      return radians * (180 / Math.PI);
   };

Math.toRadians = 
   function(degrees)
   {
      if (typeof degrees != "number")
         throw "illegal argument: " + degrees;

      return degrees * (Math.PI / 180);
   };

Math.trunc =
   function(n)
   {
      if (typeof n != "number")
         throw "illegal argument: " + n;

      return (n >= 0) ? Math.floor(n) : -Math.floor(-n);
   };
```

每个方法都需要一个数字参数，如果不是这样，就会抛出一个异常。前两个方法执行简单的角度或弧度转换，第三个方法通过`Math`的`floor()`方法截断它的参数。

当`floor()`已经执行截断时，为什么还要引入一个`trunc()`方法？当收到一个负的非整数参数时，`floor()`将这个数字向下舍入到下一个最大的负整数。例如，`floor()`将`-4.1`转换为`-5`，而不是更可取的`-4`。

以下测试用例测试了这些项目:

```
try
{
   alert("Math.toDegrees(null): " + Math.toDegrees(null));
}
catch (err)
{
   alert("null degrees not supported.");
}
alert("Math.toDegrees(Math.PI): " + Math.toDegrees(Math.PI));

try
{
   alert("Math.toRadians(null): " + Math.toRadians(null));
}
catch (err)
{
   alert("null radians not supported.");
}
alert("Math.toRadians(180): " + Math.toRadians(180));

try
{
   alert("Math.trunc(null): " + Math.trunc(null));
}
catch (err)
{
   alert("null value not supported.");
}
alert("Math.trunc(10.83): " + Math.trunc(10.83));
alert("Math.trunc(-10.83): " + Math.trunc(-10.83));
```

当您运行这些测试用例时，您应该观察到以下输出:

```
null degrees not supported.
Math.toDegrees(Math.PI): 180
null radians not supported.
Math.toRadians(180): 3.141592653589793
null value not supported.
Math.trunc(10.83): 10
Math.trunc(-10.83): -10
```

## 增加数量

`Number`对象是 64 位双精度浮点数的对象包装器。下面的代码片段展示了一个`trunc()`实例方法的实现，该方法类似于其在`Math`对象中对应的对象方法:

```
Number.prototype.trunc = 
   function()
   {
      var num = this;
      return (num < 0) ? -Math.floor(-num) : Math.floor(num);
   };
```

以下测试案例练习了这种方法:

```
alert("(25.6).trunc(): " + (25.6).trunc());
alert("(-25.6).trunc(): " + (-25.6).trunc());
alert("10..trunc(): " + 10..trunc());
```

`10..trunc()`中的两个点防止 JavaScript 解析器假设`trunc`是小数部分(当遇到`10.trunc()`时会被假设)并报告错误。为了更清楚，我可以把`10.`放在圆括号里，比如`(10.).trunc()`。

当您运行这些测试用例时，您应该观察到以下输出:

```
(25.6).trunc(): 25
(-25.6).trunc(): -25
10..trunc(): 10
```

## 扩充字符串

`String`对象是字符串的对象包装器。我已经为这个对象添加了`endsWith()`、`reverse()`和`startsWith()`方法，它们与 Java 语言中的对应方法相似。

### 实施和测试`endsWith()`和`startsWith()`

下面的代码片段展示了`endsWith()`和`startsWith()`方法的实现，这两个方法分别对后缀或前缀与字符串的结尾或开头进行区分大小写的比较:

```
String.prototype.endsWith = 
   function(suffix) 
   {
      if (typeof suffix != "string")
         throw "illegal argument" + suffix;

      if (suffix == "")
         return true;

      var str = this;
      var index = str.length - suffix.length;
      return str.substring(index, index + suffix.length) == suffix;
   };

String.prototype.startsWith = 
   function(prefix)
   {
      if (typeof prefix != "string")
         throw "illegal argument" + prefix;

      if (prefix == "")
         return true;

      var str = this;
      return str.substring(0, prefix.length) == prefix;
   };
```

每个`endsWith()`和`startsWith()`都是相似的，它首先验证它的参数是一个字符串，如果不是，抛出一个异常。当它的参数是空字符串时，它返回 true，因为空字符串总是匹配的。

每个方法还使用`String`的`substring()`方法在比较之前从字符串中提取适当的后缀或前缀。然而，它们在传递给`substring()`的开始和结束索引的计算上有所不同。

以下测试案例运用了这些方法:

```
try
{      
   alert("'abc'.endsWith(undefined): " + "abc".endsWith(undefined));
}
catch (err)
{
   alert("not a string");
}
alert("'abc'.endsWith(''): " + "abc".endsWith(""));
alert("'this is a test'.endsWith('test'): " +
      "this is a test".endsWith("test"));
alert("'abc'.endsWith('abc'): " + "abc".endsWith("abc"));
alert("'abc'.endsWith('Abc'): " + "abc".endsWith("Abc"));
alert("'abc'.endsWith('abcd'): " + "abc".endsWith("abcd"));

try
{      
   alert("'abc'.startsWith(undefined): " + "abc".startsWith(undefined));
}
catch (err)
{
   alert("not a string");
}
alert("'abc'.startsWith(''): " + "abc".startsWith(""));
alert("'this is a test'.startsWith('this'): " +
      "this is a test".startsWith("this"));
alert("'abc'.startsWith('abc'): " + "abc".startsWith("abc"));
alert("'abc'.startsWith('Abc'): " + "abc".startsWith("Abc"));
alert("'abc'.startsWith('abcd'): " + "abc".startsWith("abcd"));
```

当您运行这些测试用例时，您应该观察到以下输出:

```
not a string
'abc'.endsWith(''): true
'this is a test'.endsWith('test'): true
'abc'.endsWith('abc'): true
'abc'.endsWith('Abc'): false
'abc'.endsWith('abcd'): false
not a string
'abc'.startsWith(''): true
'this is a test'.startsWith('this'): true
'abc'.startsWith('abc'): true
'abc'.startsWith('Abc'): false
'abc'.startsWith('abcd'): false
```

### 实施和测试`reverse()`

以下代码片段展示了一个`reverse()`方法的实现，该方法反转调用该方法的字符串的字符，并返回结果字符串:

```
String.prototype.reverse = 
   function()
   {
      var str = this;
      var revStr = "";
      for (var i = str.length - 1; i >= 0; i--)
         revStr += str.charAt(i);
      return revStr;
   };
```

`reverse()`在字符串上向后循环，并将每个字符附加到一个临时字符串变量，该变量被返回。因为字符串连接开销很大，所以您可能更喜欢像`return this.split("").reverse().join("");`这样的面向数组的表达式。

下面的测试用例练习了这种方法:

```
alert("'abc'.reverse(): " + "abc".reverse());
```

当您运行这个测试用例时，您应该观察到以下输出:

```
'abc'.reverse(): cba
```

## 结论

JavaScript 使得用新的功能来扩充其核心对象变得很容易，您可能还能想到其他的例子。

我发现最简单的方法是将一个核心对象的所有新属性和方法定义放在一个单独的文件中(例如，`date.js`)，并通过一个`<script>`元素(例如，`<script type="text/javascript" src="date.js"><script>`)将该文件包含在页面的页眉中。

对于家庭作业，在`Array`对象中添加一个`shuffle()`方法来混洗一组元素(例如，扑克牌对象)。在实现中使用本文的`rnd()`方法。

## 分享这篇文章