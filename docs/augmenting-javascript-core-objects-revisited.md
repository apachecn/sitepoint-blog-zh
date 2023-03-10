# 重温扩充 JavaScript 核心对象

> 原文：<https://www.sitepoint.com/augmenting-javascript-core-objects-revisited/>

我最近的[扩充 JavaScript 核心对象](https://www.sitepoint.com/augmenting-javascript-core-objects/)的文章展示了如何向 JavaScript 的`Array`、`Boolean`、`Date`、`Math`、`Number`和`String`核心对象引入新的属性和方法。我遵循了其他文章和博客帖子的传统，包括下面列出的那些，它们展示了如何用新功能扩展这些核心对象:

*   [扩展 Math.round、Math.ceil 和 Math.floor 以实现精度](http://lea.verou.me/2009/02/extend-mathround-mathceil-and-mathfloor-to-allow-precision/)
*   [扩展 JavaScript 对象和类](http://phrogz.net/JS/classes/ExtendingJavaScriptObjectsAndClasses.html)
*   [扩展 JavaScript 的字符串对象](http://alvinabad.wordpress.com/2009/02/12/extending-javascripts-string-object/)
*   [用用户定义的方法扩展 JavaScript 日期对象](http://www.digital-web.com/articles/javascript_date_object_with_user_methods/)
*   [JavaScript 数组包含](http://css-tricks.com/snippets/javascript/javascript-array-contains/)

直接给核心对象或其原型添加属性是有争议的。在他的[扩展 JavaScript Natives](http://javascriptweblog.wordpress.com/2011/12/05/extending-javascript-natives/) 博客文章中，Angus Croll 提出了这种方法的几个问题。例如，未来的浏览器版本可能会实现一个高效的属性或方法，但会被一个低效的自定义属性/方法所破坏。阅读 Croll 的博客文章，了解更多关于这个问题和其他问题的信息。

因为核心对象增强是强大而优雅的，所以应该有一种方法来利用这个特性，同时避免它的问题。幸运的是，有一种方法可以完成这个任务，通过利用[适配器设计模式](http://en.wikipedia.org/wiki/Adapter_pattern)，也称为包装器模式。在本文中，我介绍了我的库的一个新版本，它使用包装器来增加各种核心对象，而实际上并没有增加它们。

## 探索新的核心对象增强库

我的新核心对象增强库试图通过利用 [JavaScript 模块模式](http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html)来最小化它对全局名称空间的影响，该模式将所有库代码放在一个匿名闭包中。这个库目前导出围绕着`Date`和`Math`的`_Date`和`_Math`对象，并通过查询`ca_tutortutor_AJSCOLib`全局变量来访问。

**关于`ca_tutortutor_AJSCOLib`**
`ca_tutortutor_AJSCOLib`全局变量提供对增强库的访问。为了尽量减少与另一个全局变量的名称冲突，我在`AJSCOLib`前面加上了我的反向互联网域名。

清单 1 展示了我的库的内容，它存储在一个`ajscolib.js`脚本文件中。

```
var ca_tutortutor_AJSCOLib = 
   (function()
   {
      var my = {};

      var _Date_ = Date;

      function _Date(year, month, date, hours, minutes, seconds, ms)
      {
         if (year === undefined)
            this.instance = new _Date_();
         else
         if (month === undefined)
            this.instance = new _Date_(year);
         else
         if (hours === undefined)
            this.instance = new _Date_(year, month, date);
         else
            this.instance = new _Date_(year, month, date, hours, minutes, seconds, 
                                       ms);

         this.copy = 
            function()
            {
               return new _Date_(this.instance.getTime());
            };

         this.getDate =
            function()
            {
               return this.instance.getDate();
            };

         this.getDay =
            function()
            {
               return this.instance.getDay();
            };

         this.getFullYear =
            function()
            {
               return this.instance.getFullYear();
            };

         this.getHours =
            function()
            {
               return this.instance.getHours();
            };

         this.getMilliseconds =
            function()
            {
               return this.instance.getMilliseconds();
            };

         this.getMinutes =
            function()
            {
               return this.instance.getMinutes();
            };

         this.getMonth =
            function()
            {
               return this.instance.getMonth();
            };

         this.getSeconds =
            function()
            {
               return this.instance.getSeconds();
            };

         this.getTime =
            function()
            {
               return this.instance.getTime();
            };

         this.getTimezoneOffset =
            function()
            {
               return this.instance.getTimezoneOffset();
            };

         this.getUTCDate =
            function()
            {
               return this.instance.getUTCDate();
            };

         this.getUTCDay =
            function()
            {
               return this.instance.getUTCDay();
            };

         this.getUTCFullYear =
            function()
            {
               return this.instance.getUTCFullYear();
            };

         this.getUTCHours =
            function()
            {
               return this.instance.getUTCHours();
            };

         this.getUTCMilliseconds =
            function()
            {
               return this.instance.getUTCMilliseconds();
            };

         this.getUTCMinutes =
            function()
            {
               return this.instance.getUTCMinutes();
            };

         this.getUTCMonth =
            function()
            {
               return this.instance.getUTCMonth();
            };

         this.getUTCSeconds =
            function()
            {
               return this.instance.getUTCSeconds();
            };

         this.getYear =
            function()
            {
               return this.instance.getYear();
            };

         this.isLeap = 
            function()
            {
               var year = this.instance.getFullYear();
               return (year % 400 == 0) || (year % 4 == 0 && year % 100 != 0);
            };

         _Date.isLeap =  
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               var year = date.getFullYear();
               return (year % 400 == 0) || (year % 4 == 0 && year % 100 != 0);
            };

         this.lastDay = 
            function()
            {  
               return new _Date_(this.instance.getFullYear(), 
                                 this.instance.getMonth() + 1, 0).getDate();
            };

         _Date.monthNames = ["January", "February", "March", "April", "May",
                             "June", "July", "August", "September", "October",
                             "November", "December"];

         _Date.parse =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               return _Date_.parse(date);
            };

         this.setDate =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               this.instance.setDate(date);
            };

         this.setFullYear =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               this.instance.setFullYear(date);
            };

         this.setHours =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               this.instance.setHours(date);
            };

         this.setMilliseconds =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               this.instance.setMilliseconds(date);
            };

         this.setMinutes =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               this.instance.setMinutes(date);
            };

         this.setMonth =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               this.instance.setMonth(date);
            };

         this.setSeconds =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               this.instance.setSeconds(date);
            };

         this.setTime =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               this.instance.setTime(date);
            };

         this.setUTCDate =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               this.instance.setUTCDate(date);
            };

         this.setUTCFullYear =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               this.instance.setUTCFullYear(date);
            };

         this.setUTCHours =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               this.instance.setUTCHours(date);
            };

         this.setUTCMilliseconds =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               this.instance.setUTCMilliseconds(date);
            };

         this.setUTCMinutes =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               this.instance.setUTCMinutes(date);
            };

         this.setUTCMonth =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               this.instance.setUTCMonth(date);
            };

         this.setUTCSeconds =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               this.instance.setUTCSeconds(date);
            };

         this.toDateString =
            function()
            {
               return this.instance.toDateString();
            };

         this.toISOString =
            function()
            {
               return this.instance.toISOString();
            };

         this.toJSON =
            function()
            {
               return this.instance.toJSON();
            };

         this.toLocaleDateString =
            function()
            {
               return this.instance.toLocaleDateString();
            };

         this.toLocaleTimeString =
            function()
            {
               return this.instance.toLocaleTimeString();
            };

         this.toString = 
            function()
            {
               return this.instance.toString();
            };

         this.toTimeString =
            function()
            {
               return this.instance.toTimeString();
            };

         this.toUTCString =
            function()
            {
               return this.instance.toUTCString();
            };

         _Date.UTC =
            function(date)
            {
               if (date instanceof _Date)
                  date = date.instance;
               return _Date_.UTC(date);
            };

         this.valueOf =
            function()
            {
               return this.instance.valueOf();
            };
      }
      my._Date = _Date;

      var _Math = {};

      var props = Object.getOwnPropertyNames(Math);
      props.forEach(function(key)
      {
         if (Math[key]) 
            _Math[key] = Math[key]; 
      });

      if (!_Math.GOLDEN_RATIO)
         _Math.GOLDEN_RATIO = 1.61803398874;

      if (!_Math.rnd || _Math.rnd.length != 1)
         _Math.rnd = 
            function(limit)
            {
               if (typeof limit != "number")
                  throw "illegal argument: " + limit;

               return Math.random() * limit | 0;
            };

      if (!_Math.rndRange || _Math.rndRange.length != 2)
         _Math.rndRange = 
            function(min, max)
            {
               if (typeof min != "number")
                  throw "illegal argument: " + min;

               if (typeof max != "number")
                  throw "illegal argument: " + max;

               return Math.floor(Math.random() * (max - min + 1)) + min;
            };

      if (!_Math.toDegrees || _Math.toDegrees.length != 1)
         _Math.toDegrees = 
            function(radians)
            {
               if (typeof radians != "number")
                  throw "illegal argument: " + radians;

               return radians * (180 / Math.PI);
            };

      if (!_Math.toRadians || _Math.toRadians.length != 1)
         _Math.toRadians = 
            function(degrees)
            {
               if (typeof degrees != "number")
                  throw "illegal argument: " + degrees;

               return degrees * (Math.PI / 180);
            };

      if (!_Math.trunc || _Math.trunc.length != 1)
         _Math.trunc =
            function(n)
            {
               if (typeof n != "number")
                  throw "illegal argument: " + n;

               return (n >= 0) ? Math.floor(n) : -Math.floor(-n);
            };
      my._Math = _Math;

      return my;
   }());
```

**清单 1:** 这个自包含的扩充库可以扩展到支持所有核心对象

匿名闭包内声明的所有变量和函数都是该闭包的局部变量。要从闭包外部访问，必须导出一个变量或函数。要导出变量或函数，只需将它添加到一个对象中，并从闭包返回该对象。在清单 1 中，该对象被称为`my`，并被赋予一个`_Date`函数引用和一个`_Math`对象引用。

在声明初始化为空对象的变量`my`之后，清单 1 声明了引用`Date`核心对象的变量`_Date_`。当我需要从图书馆内部访问`Date`时，我会引用`_Date_`而不是`Date`。我将在本文后面解释我这样做的原因。

清单 1 现在声明了一个用于构造`_Date`包装器对象的`_Date`构造函数。这个构造函数声明了与`Date`核心对象相同的`year`、`month`、`date`、`hours`、`minutes`、`seconds`和`ms`参数。这些参数被询问以确定调用哪个`Date`构造函数的变体:

*   `_Date()`调用`Date()`将`Date`对象初始化为当前日期。通过测试`undefined`的`year`来检测这种情况。

*   `_Date(year)`调用`Date(milliseconds)`或`Date(dateString)`将`Date`对象初始化为指定的毫秒数或日期字符串——我让`Date`来处理这两种情况。通过测试`undefined`的`month`来检测这种情况。

*   `_Date(year, month, date)`调用`_Date(year, month, date)`将`Date`对象初始化为指定的年月日(日期)。通过测试`undefined`的`hour`来检测这种情况。

*   `_Date(year, month, day, hours, minutes, seconds, milliseconds)`调用`Date(year, month, day, hours, minutes, seconds, milliseconds)`将`Date`对象初始化为各个组件描述的日期。这种情况是默认的。

不管调用哪一个*构造函数变体*(具有全部或更少参数的构造函数调用)，返回的结果都存储在`_Date`的`instance`属性中。你不应该直接访问`instance`，因为将来`Date`引入`instance`属性时，你可能需要重命名这个属性。不访问库外部的`instance`减少了代码维护。

此时，清单 1 注册了新的`copy()`、`isLeap()`和`lastDay()`方法，并用`_Date`注册了新的`monthNames`属性。它还注册了`Date`的方法。前一种方法用与`_Date`而不是`Date`相关联的新功能来扩充`Date`，如下所述。后一种方法使用`instance`来访问先前存储的`Date`实例，通常是为了调用它们的`Date`副本:

*   `copy()`创建调用该方法的`Date`对象实例的副本。换句话说，它克隆了`Date`实例。示例:`var d = new Date(); var d2 = d.copy();`

*   当调用的`Date`对象实例的年份部分表示闰年时，`isLeap()`返回 true 否则，返回错误。例子:`var d = new Date(); alert(d.isLeap());`

*   当`date`的年份部分表示闰年时，`isLeap(date)`返回 true 否则，返回错误。例子:`alert(Date.isLeap(new Date()));`

*   `lastDay()`返回调用`Date`对象实例的月份的最后一天。例子:`var d = new Date(); alert(d.lastDay());`

*   虽然不是一个方法，但是您可以从`Date.monthNames`数组属性中获得一个基于英语的长月份名称。传递范围从 0 到 11 的索引。示例:`alert(Date.monthNames[0])`

与`_Date`而不是其实例相关联的方法被直接分配给`_Date`，如在`_Date.UTC = function(date)`中。`date`参数标识核心`Date`对象参考或`_Date`参考。与`_Date`实例相关联的方法被分配给`this`。在该方法中，通过`this.instance`访问`Date`实例。

您将遵循之前的协议来支持`Array`、`String`和其他核心对象——除了`Math`。与其他核心对象不同，您不能构造`Math`对象。相反，`Math`只是一个用于存储静态属性和方法的占位符。出于这个原因，我通过声明一个初始化为空对象的`_Math`变量并直接给这个对象分配属性和方法来区别对待`Math`。

初始化`_Math`的第一步是调用`Object`的`getOwnPropertyNames()`方法(在 ECMAScript 5 中实现，受现代桌面浏览器支持)返回直接在参数对象上找到的所有属性(可枚举或不可枚举)的数组，即`Math`。清单 1 然后在引入新的属性/方法(当还没有出现时)之前将每个属性(函数或其他)分配给`_Math`:

*   `GOLDEN_RATIO`是我在上一篇文章中提到的黄金比例的常数。示例:`alert(Math.GOLDEN_RATIO);`

*   `rnd(limit)`返回从 0 到比`limit`的值小 1 的整数。例子:`alert(Math.rnd(10));`

*   `rndRange(min, max)`返回一个范围从`min`的值到`max`的值的随机整数。示例:`alert(Math.rndRange(10, 20));`

*   `toDegrees(radians)`将`radians`值转换为以度为单位的等效值，并返回该值。例子:`alert(Math.toDegrees(Math.PI));`

*   `toRadians(degrees)`将`degrees`值转换为以弧度表示的等效值，并返回该值。例子:`alert(Math.toRadians(180));`

*   `trunc(n)`从传递给`n`的正数或负数中移除小数部分，并返回整数部分。例子:`alert(Math.trunc(5.8));`

当每个方法检测到不属于`Number`类型的参数时，都会抛出一个表示非法参数的异常。

为什么要费心创建一个增强库，而不是创建单独的实用程序对象(如`DateUtil`或`MathUtil`)。这个库充当了一个巨大的[垫片](http://en.wikipedia.org/wiki/Shim_(computing))来提供跨浏览器的一致功能。例如，Firefox 25.0 的`Math`对象公开了一个`trunc()`方法，而 Opera 12.16 却没有这个方法。我的库确保了一个`trunc()`方法总是可用的。

## 测试和使用新的核心对象增强库

既然你已经有机会探索图书馆，你会想要尝试一下。我已经创建了一对测试各种新的`_Date`和`_Math`功能的脚本，还创建了一对更实用的脚本来更充分地使用这个库。清单 2 展示了一个 HTML 文档，其中嵌入了一个用于测试`_Date`的脚本。

```
<!DOCTYPE html>
<html>
  <head>
    <title>
      Augmented Date Tester
    </title>

    <script type="text/javascript" src="ajscolib.js">
    </script>
  </head>

  <body>
    <script>
    var Date = ca_tutortutor_AJSCOLib._Date;

    var date = new Date();
    alert("Current date: " + date);
    alert("Current date: " + date.toString());
    var dateCopy = date.copy();
    alert("Copy of current date: " + date.toString());
    alert("Current date == Copy of current date: " + (date == dateCopy));
    alert("Isleap " + date.toString() + ": " + date.isLeap());
    alert("Isleap July 1, 2012: " + Date.isLeap(new Date(2012, 6, 1)));
    alert("Last day: "+ date.lastDay());
    alert("Month names: " + Date.monthNames);
    </script>
  </body>
</html>
```

**清单 2:** 测试“增强的”`Date`对象

当您使用这个库时，您不会想要指定`ca_tutortutor_AJSCOLib._Date`，也可能不会想要指定`_Date`。相反，你会想要指定`Date`，就好像你正在处理核心对象本身一样。你不应该改变你的代码来改变对其他东西的引用。幸运的是，你不必那样做。

脚本中的第一行将`ca_tutortutor_AJSCOLib._Date`分配给`Date`，有效地删除了对`Date`核心对象的所有访问。这就是在库中指定`var _Date_ = Date;`的原因。如果我在库代码中引用了`Date`而不是`_Date_`，您会发现“太多的递归”(可能还有其他问题)。

代码的其余部分对那些使用过`Date`的人来说很熟悉。然而，有一个小问题。当你调用`alert("Current date: " + date);`时会得到什么输出？如果您使用的是`Date`核心对象，您将会看到`Current date:` 后跟当前日期的字符串表示。然而，在当前的上下文中，您会观察到`Current date:` 后跟一个毫秒数值。

**`toString()`与`valueOf()`**
查看 JavaScript 中的[对象到原语的转换，了解为什么`alert("Current date: " + date);`会产生一个字符串或数字表示的`date`。](http://www.adequatelygood.com/Object-to-Primitive-Conversions-in-JavaScript.html)

让我们将" augmented" `Date`对象用于一些实际用途，比如创建一个日历页面。该脚本将使用`document.writeln()`根据`<table>`元素输出该页面的 HTML。将使用`_Date`构造函数的两个变体以及`getFullYear()`、`getMonth()`、`getDay()`、`lastDay()`和`getDate()`方法，以及`monthNames`属性。查看清单 3。

```
<!DOCTYPE html>
<html>
  <head>
    <title>
      Calendar
    </title>

    <script type="text/javascript" src="ajscolib.js">
    </script>
  </head>

  <body>
    <script>
    var Date = ca_tutortutor_AJSCOLib._Date;

    var date = new Date();
    var year = date.getFullYear();
    var month = date.getMonth();
    document.writeln("<table border=1>");
    document.writeln("<th bgcolor=#eeaa00 colspan=7>");
    document.writeln("" + Date.monthNames[month] + " " + year + 
                     "");
    document.writeln("</th>");
    document.writeln("<tr bgcolor=#ff7700>");
    document.writeln("<td><b>S</b></td>");
    document.writeln("<td><b>M</b></td>");
    document.writeln("<td><b>T</b></td>");
    document.writeln("<td><b>W</b></td>");
    document.writeln("<td><b>T</b></td>");
    document.writeln("<td><b>F</b></td>");
    document.writeln("<td><b>S</b></td>");
    document.writeln("</tr>");
    var dayOfWeek = new Date(year, month, 1).getDay();
    var day = 1;
    for (var row = 0; row < 6; row++)
    {
       document.writeln("<tr>");
       for (var col = 0; col < 7; col++)
       {
          var row;
          if ((row == 0 && col < dayOfWeek) || day > date.lastDay())
          {
             document.writeln("<td bgcolor=#cc6622>");
             document.writeln(" ");
          }
          else
          {
             if (day == date.getDate())
                document.writeln("<td bgcolor=#ffff00>");
             else
             if (day % 2 == 0)
                document.writeln("<td bgcolor=#ff9940>");
             else
                document.writeln("<td>");
             document.writeln(day++);
          }
          document.writeln("</td>");
       }
       document.writeln("</tr>");
    }
    document.writeln("</table>");
    </script>
  </body>
</html>
```

**清单 3:** 使用“增强的”`Date`对象生成一个日历页面

为了创建一个真实的日历页面，我们需要知道一个月的第一天是在一周的哪一天。表达式`new Date(year, month, 1).getDay()`给出了我们想要的信息(0 表示周日，1 表示周一，依此类推)，这些信息被分配给`dayOfWeek`。首行中列索引小于`dayOfWeek`的每个方块都保留为空白。

图 1 显示了一个示例日历页面。


![The current day is highlighted in yellow.](img/f2f5a91bd8267e326ade7ec12e06c42e.png)

**图 1:** 当天用黄色突出显示。



清单 4 展示了一个 HTML 文档，其中嵌入了一个用于测试`_Math`的脚本。

```
<!DOCTYPE html>
<html>
  <head>
    <title>
      Augmented Math Tester
    </title>

    <script type="text/javascript" src="ajscolib.js">
    </script>
  </head>

  <body>
    <script>
    var Math = ca_tutortutor_AJSCOLib._Math;

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

    for (var i = 0; i < 10; i++)
       alert(Math.rndRange(5, 9));

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
    </script>
  </body>
</html>
```

**清单 4:** 测试“增强的”`Math`对象

让我们将“增强的”`Math`对象用于一些实际用途，例如显示一条 [*心形曲线*](http://en.wikipedia.org/wiki/Cardioid) ，这是一条由圆周上的一个点描绘的平面曲线，该点围绕一个相同半径的固定圆滚动。该脚本将使用`Math`的`rndRange()`、`toRadians()`、`cos()`和`sin()`方法。查看清单 5。

```
<!DOCTYPE html>
<html>
  <head>
    <title>
      Cardioid
    </title>

    <script type="text/javascript" src="ajscolib.js">
    </script>
  </head>

  <body>
    <canvas id="canvas" width="300" height="300">
    canvas not supported
    </canvas>

    <script>
    var Math = ca_tutortutor_AJSCOLib._Math;

    var canvas = document.getElementById("canvas");
    var canvasctx = canvas.getContext("2d");

    var width = document.getElementById("canvas").width;
    var height = document.getElementById("canvas").height;

    canvasctx.fillStyle = "#000";
    canvasctx.fillRect(0, 0, width, height);
    canvasctx.fillStyle = "RGB(" + Math.rndRange(128, 255) + "," +
                          Math.rndRange(128, 255) + "," +
                          Math.rndRange(128, 255) + ")";

    canvasctx.beginPath();
    for (var angleDeg = -180.0; angleDeg < 180.0; angleDeg += 0.1)
    {
       var angle = Math.toRadians(angleDeg);

       // Evaluate cardioid curve equation. This produces radius for
       // given angle. Note: [r, angle] are the polar coordinates.

       var r = 60.0 + 60.0 * Math.cos(angle);

       // Convert polar coordinates to rectangular coordinates. Add
       // width / 2 and height / 2 to move curve's origin to center
       // of canvas. (Origin defaults to canvas's upper-left corner.)

       var x = r * Math.cos(angle) + width / 2;
       var y = r * Math.sin(angle) + height / 2;
       if (angle == 0.0)
          canvasctx.moveTo(x, y);
       else
          canvasctx.lineTo(x, y)
    }
    canvasctx.closePath();
    canvasctx.fill();
    </script>
  </body>
</html>
```

**清单 5:** 使用“增强的”`Math`对象生成一条心形曲线

清单 5 使用 HTML5 的 canvas 元素和 API 来呈现心形曲线，该曲线通过 canvas 上下文的`beginPath()`、`moveTo()`、`lineTo()`和`closePath()`方法构建成一个多边形。曲线填充颜色的每个部分都是通过`rndRange()`随机选择的。它的参数确保组件不会太暗。曲线通过画布上下文的`fill()`方法填充。

图 2 显示了一条彩色的心形曲线。



![Reload the page to change the curve's color.](img/db9506503c02305a7da2543464949195.png)

**图 2:** 重新加载页面来改变曲线的颜色。



## 结论

本文展示了如何创建一个库来增加 JavaScript 的核心对象，而不直接增加它们。该库的公共接口可跨浏览器移植，尽管出于兼容性、性能或其他原因，实现可能需要调整。作为练习，将我以前的增强文章的`Array`、`Boolean`、`Number`和`String`增强添加到这个库中。

## 分享这篇文章