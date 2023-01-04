# JavaScript Date 对象:JavaScript 日期和时间初学者指南

> 原文：<https://www.sitepoint.com/beginners-guide-to-javascript-date-and-time/>

日期和时间值用于许多重要的功能，如搜索、验证和报告生成。web 应用程序中的日期没有标准格式，因为不同时区和国家的人喜欢不同的格式。因此，对于前端开发人员来说，适应任何类型的日期格式都是一个挑战。今天，我们将看看如何在 JavaScript 中有效地使用日期和时间函数。

## 开始使用`Date`对象

基本的`Date`构造函数没有参数，被初始化为当前日期，如下所示。

我们可以清楚地看到，结果日期取决于您当地的时区，因为显示了两个不同的时区。

```
var today = new Date();
// Thu Feb 27 2013 15:15:25 GMT+1000 (EST)
// Thu Feb 27 2013 10:45:25 GMT+0530 (IST)
```

使用`Date`对象有两种主要方式:

*   通过将格式或特定日期作为参数提供给`Date`构造函数，可以生成自定义日期。

    ```
    date1 = new Date ( "January 6, 2013" );
    ```

*   然后，我们可以使用几十个内置函数来处理创建的日期。下面的代码向您展示了如何使用可用的函数提取日期的每个组成部分。

    ```
    date1 = new Date ( "January 6, 2013" );
    date = date1.getDate();
    year = date1.getFullYear();
    month = date1.getMonth();
    ```

让我们讨论一下使用日期函数时应该注意的一些基本事项。通常，我们将使用日期、月份和年份组件。日期可以在 1 到 31 之间，月份可以在 0 到 11 之间。下面的代码创建了一个`Date`对象，并将一个月中的某一天设置为 20 号。

```
date1 = new Date ();
date1.setDate(20);
```

您也可以使用不在上述范围内的数字来生成未来或过去的日期。考虑下面的例子，它使用了超出指定范围的值。

```
date1 = new Date ();
date1.setDate(-1);
date1.setMonth(-1);
```

假设当前日期是 2013 年 2 月 20 日。上述代码将更改为上一年中上个月的倒数第二天，即 2012 年 12 月 30 日。同样，您可以使用大于 31 的日期值和大于 11 的月份值来生成未来的日期。已经学习了如何使用`Date`对象通过各种方法生成日期，让我们看看如何格式化日期。

## 日期格式

日期没有单一的通用格式，我们需要根据不同的情况用不同的格式显示日期串。基本上，这是一个双向过程，我们必须先将字符串转换成一个`Date`对象，然后再将其转换回我们喜欢的格式。初始过程包括将用户输入的字符串转换成一个`Date`对象。让我们考虑一些常见的用户日期输入方法。

过去，我们有纯文本字段或三个选项字段来选择月、日和年。如今，用户输入元素已得到显著改进，日期选择器已成为选择日期的现代方式。除了日期选择器，JavaScript 日历是一个广泛使用的组件，我们必须在这个初始过程中考虑它。这些组件中的日期以字符串形式检索。如果日期字符串包含通用格式，我们可以通过传递输入字段值来创建`Date`对象，如下所示。

```
// Assume date is 2013-02-15
var current = $("start_date").value();
var date1=new Date(current);
```

如果使用日历，日期将被指定为 HTML5 数据属性，而不是 value 属性。这方面的一个例子如下所示。

```
 `var current = $("start_date").attr("date-current");
var date1=new Date(current);`

大多数日期选择器和日历都提供了广泛的预定义格式供您使用。但是，如果组件没有提供通用的格式，或者日期被指定为代码，那么我们必须使用如下所示的函数手动构造`Date`对象。

```
var month = {"JAN":"01","FEB":"02"};
var current = "2013-FEB-22";
var date_components = current.split("-");
var current_year = date_components[0];
var current_month= month[date_components[1].toString()];
var current_day = date_components[2];

current = current_year+"-"+current_month+"-"+current_day;
var date11=new Date(current);
document.write(date1);
```

因为我们知道每个组件的含义，所以可以拆分日期字符串，并构造一个通用格式来解析`Date`对象。

现在，我们已经将用户提供的日期字符串转换成 JavaScript `Date`对象，以处理验证、比较和应用程序所需的任何其他内容。最后，我们必须将它转换回字符串，以保存它或在 web 浏览器中显示它。这个转换比前一个容易得多，因为我们可以使用函数来分解组件。我认为你应该有一个通用的转换函数，以便在所有项目中重用。让我们开发一个简单的函数，将日期转换成各种字符串格式。

```
var date=new Date();
var format = "YYYY-MMM-DD DDD";
document.write(dateConvert(date,format));

function dateConvert(dateobj,format){
  var year = dateobj.getFullYear();
  var month= ("0" + (dateobj.getMonth()+1)).slice(-2);
  var date = ("0" + dateobj.getDate()).slice(-2);
  var hours = ("0" + dateobj.getHours()).slice(-2);
  var minutes = ("0" + dateobj.getMinutes()).slice(-2);
  var seconds = ("0" + dateobj.getSeconds()).slice(-2);
  var day = dateobj.getDay();
  var months = ["JAN","FEB","MAR","APR","MAY","JUN","JUL","AUG","SEP","OCT","NOV","DEC"];
  var dates = ["SUN","MON","TUE","WED","THU","FRI","SAT"];
  var converted_date = "";

  switch(format){
    case "YYYY-MM-DD":
      converted_date = year + "-" + month + "-" + date;
      break;
    case "YYYY-MMM-DD DDD":
      converted_date = year + "-" + months[parseInt(month)-1] + "-" + date + " " + dates[parseInt(day)];
      break;
  }

  return converted_date;
}
```

这里，我们有一个函数，它将一个`Date`对象和期望的格式作为参数。在函数内部，所有日期部分都使用函数分配到变量中。然后，我们有一个`switch`语句来选择格式。这种可重用的效用函数对于开发应用程序非常有效。当要求你提供新的格式时，只需在`switch`语句中添加另一个条目，并根据新的格式返回组件。代码的输出将是`2013-FEB-17 SUN`。同样，您可以将其转换成自己喜欢的显示格式。

## 日期比较

在给定的日期范围内搜索记录是日期在 web 应用程序中最常见的用途之一。首先，在考虑比较之前，我们必须将用户提供的日期字符串转换成一个`Date`对象。让我们比较用不同方法生成的两个日期，如下所示。使用`getTime()`方法比较日期是明智的。假设这些日期都是在 2013 年 2 月 18 日生成的。

```
var date1=new Date();
var date2=new Date("2013-02-18");

if(date1.getTime() == date2.getTime()){
  console.log("Dates are equal");
}
```

即使两个对象包含相同的日期，它们也不相等。`date1`包含当前日期，并将包括当前时间。但是，`date2`将不包含当前时间，因此它不匹配。

解决方案是将两个对象的时间都设置为标准时间格式。我见过很多开发者把当前日期对象的时间设置为 00:00:00，如下图。

```
var date1=new Date();
date1.setHours(0,0,0,0);
var date2=new Date("2013-02-18");
```

所以，`date1`会类似于`Mon Feb 18 2013 00:00:00 GMT+0530 (IST)`。但是，它仍然不匹配，因为`date2`将包含您当地时区的时间，对我来说是 GMT+5.30。如果不想考虑时间，最好的方法是将两个日期设置成相同的时间，如下所示。

```
var date1=new Date();
date1.setHours(0,0,0,0);
var date2=new Date("2013-02-17");
date2.setHours(0,0,0,0);
```

我们也可以使用相同的技术来比较日期范围。请确保将两个日期的所有其他部分设置为相同的值，并且只检查两个日期中不同的部分。

## 我们应该使用日期操作库吗？

在整篇文章中，我们讨论了`Date`对象是如何工作的，以及我们可以用来操作日期的必要函数。选择日期操作库是您必须根据自己的需求来决定的事情。简单的数据处理不需要库也能轻松完成。但是，如果您想要复杂的功能，我建议您使用像 [Datejs](http://www.datejs.com/) 这样的日期操作库，它提供了通过嵌套函数调用实现复杂功能的能力。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [Jump Start JavaScript](https://learnable.com/books/jump-start-javascript?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink) 。 
 *对本文的评论均关闭。有关于话题的问题吗？为什么不在我们的[论坛](https://www.sitepoint.com/community/c/javascript/33)上问呢？*

## 分享这篇文章

```