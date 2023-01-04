# 最好的日期格式是什么？

> 原文：<https://www.sitepoint.com/whats-the-best-date-format/>

在显示日期和时间时，没有一种格式适用于所有情况。由于格式的国际和地区差异如此之大，使用日期和时间的情况如此之多，每种情况都是独一无二的——从冗长而抒情的句子，如 3 月 21 日星期一上午 12:18(美国东部时间)，到神秘而简洁的输出，如今天的<q>。</q>

但是当涉及到以编程方式处理日期时——存储、比较和排序——真的不需要这么多种格式。事实上，*统一*程序日期的使用，**对所有事情使用相同的格式**更有意义。

那么用什么格式最好呢？

它需要勾选几个框——把这些想成是*‘必备’*特性:

*   它存储完整的日期和时间，提供我们创建任何其他格式所需的所有原始信息
*   我们可以使用它作为创建和转换日期的输入——例如，它必须与 JavaScript 的`new Date`构造函数的时间戳参数兼容
*   它总是使用相同的固定时区，为比较提供一个参考框架

除了这些核心要求之外，还有一些*“想要变得更好”*的特性:

*   它的格式是不需要额外转换就可以自然排序的(即如果用作文件名，它将创建一个按时间排序的顺序)
*   它没有内部空白——以避免解析歧义，例如过多的连续空格或制表符/空格差异
*   它至少提供了某种程度的可读性，主要是为了程序员的缘故
*   它应该给我们至少一些“免费”的信息，即。通过字符串解析可以轻松获得年份或小时等值，而不必每次都创建和解析新的日期对象

## 时区呢？

没有必要在不同的时区存储日期和时间——每个给定的时间戳都有一个对应的 <abbr title="Universal Co-ordinated Time">UTC</abbr> / <abbr title="Greenwich Mean Time">GMT</abbr> 。所以最简单的做法是**在<abbr title="Universal Co-ordinated Time">UTC</abbr>中记录所有时间戳，只有在输出到客户端时才将其转换为本地时间。这为我们提供了一个通用的参考框架，用于以编程方式比较日期，而不会丢失我们国际化所需的任何信息。**

**JavaScript dates are always local** **因为 JavaScript 在客户机上求值，所以它使用客户机的时钟作为时间参考。所以*任何通过`Date`构造函数传递的*时间戳都被**自动转换成用户的当地时间**。**  **## 我的最终建议

过去，在我真正坐下来认真思考这个问题之前，我通常使用 [RFC 2822](http://www.faqs.org/rfcs/rfc2822.html) 日期格式(例如`"Mon, 21 Mar 2011 00:18:56 +0000"`)——主要是因为它很容易在 <abbr title="HyperText Pre-Processor">PHP</abbr> (由格式化字符`"r"`定义)中生成，并且它勾选了所有必备的框。但它本身并不可排序，也不是我最终的选择:

**我最终推荐的是 <abbr title="Universal Co-ordinated Time">UTC</abbr> 的 [ISO 8601 (2004)](http://en.wikipedia.org/wiki/ISO_8601) 。**

这种格式有几种变体，我特别选择的一种使用日期时间分隔符`"T"`和时区标志符`"Z"`(它指定了<abbr title="Universal Co-ordinated Time">UTC</abbr>)；其他变体使用空格作为分隔符，或者将时区指定为`"+00:00"`，但是我不推荐这两种方式——空格使得它与 Firefox 中的`new Date`不兼容，并且更长更复杂的时区指示符完全没有必要。

所以我们所说的时间戳看起来是这样的:`"2011-03-21T00:18:56Z"`

虽然这种格式不像其他格式那么容易生成(见下面的 [<cite>如何使用 ISO 格式</cite>](#generation) )，但它确实勾选了所有其他的框，包括必须拥有的和想要成为最好的。它还提供了许多额外的好处——你可能会说，这些额外的功能巩固了它作为理想选择的地位:

*   它总是一个恒定的长度
*   这是 <abbr title="JavaScript Object Notation">JSON</abbr> 本身使用的格式(例如，在 JavaScript 中，如果你通过`JSON.stringify`传递一个`Date`对象，它就被转换成这种格式)
*   这是 W3C 推荐的格式和概要文件
*   它与 <abbr title="Structured Query Language">SQL</abbr> 中的`DATESTAMP`列类型兼容，尽管是“宽松”的语法

**MySQL DATESTAMP gotcha** **我在 <abbr title="HyperText Pre-Processor">PHP</abbr> 和 MySQL 中发现的一个问题是，当这种格式的时间戳存储在`DATESTAMP`列中时，**的`"T"`分隔符被替换为空格，而它的`"Z"`标记被移除**，转换为严格的`DATESTAMP`格式。但这不是我们想要的，也不可接受。

我所做的就是重新转换输出，但是我很乐意听到一个更永久的解决方案——MySQL 配置选项或其他什么？**  **## 如何使用 ISO 格式

<abbr title="HyperText Pre-Processor">PHP</abbr> 没有预定义的常量或标记来创建这种精确的格式，尽管格式化标记`"c"`创建了一个非常相似的(相同的标准，不同的变体)。然后，我们可以用字符串替换来转换差异；但是我认为最简单的方法是手工编译它，从单独的日期和时间部分，使用 [gmdate](http://www.php.net/manual/en/function.gmdate.php) 函数得到 <abbr title="Universal Co-ordinated Time">UTC</abbr> :

```
$timestamp = gmdate('Y-m-dTH:i:sZ');
```

一旦我们有了时间戳，我们就可以在客户机上使用它作为`new Date`的时间戳参数。例如，我们可以首先将原始时间戳输出到静态的 <abbr title="HyperText Markup Language">HTML</abbr> :

```
<dl class="listing">
    <dt>Date created:</dt>
    <dd id="listing-timestamp"><?php echo gmdate('Y-m-dTH:i:sZ'); ?></dd>
</dl>
```

然后使用 JavaScript 将其转换为重新格式化的特定于地区的日期和时间，例如:

```
var dd = document.getElementById("listing-timestamp");

dd.innerHTML = new Date(dd.innerHTML).toLocaleString();
```

当然，在客户机和服务器之间传递时间戳的机制会随着应用程序的不同而有很大的不同。一个 <abbr title="HyperText Pre-Processor">PHP</abbr> 脚本可以将时间戳直接输出到服务器生成的 JavaScript 它可能是 Ajax 请求的响应，也可能被写入静态的 <abbr title="HyperText Markup Language">HTML</abbr> ，就像上面的例子。无论如何，最常见的用例可能是**服务器生成的时间戳，这些时间戳输出到客户端进行转换**，我到目前为止描述的流程将会完成这一任务。但是其他情况呢？

您可能完全在服务器上工作，并且只输出完全格式化的、用户就绪的日期。 <abbr title="HyperText Pre-Processor">PHP</abbr> 提供了多种在内部将一个日期转换成另一个日期的方法，但是我发现最直接的方法是通过 [strtotime](http://www.php.net/manual/en/function.strtotime.php) 传递时间戳(将其转换成一个数值)，然后使用它作为 [date](http://www.php.net/manual/en/function.date.php) 的第二个参数:

```
$rfcdate = date('r', strtotime($timestamp));
```

相反，您可能完全在客户机上生成和转换，在这种情况下，您需要一种用 JavaScript 生成时间戳的方法。下面的函数将完成这项工作:

```
function getTimestamp()
{
    var dateobj = new Date();
    dateobj.setTime(dateobj.getTime() + (dateobj.getTimezoneOffset() * 60000));

    var datetime = {
        date : [
            dateobj.getFullYear(),    
            dateobj.getMonth() + 1,    
            dateobj.getDate()
            ],
        time : [
            dateobj.getHours(),        
            dateobj.getMinutes(),        
            dateobj.getSeconds()
            ]
        }; 

    for(var key in datetime)
    {
        if(!datetime.hasOwnProperty(key)) { continue; }

        for(var i in datetime[key])
        {
            if(!datetime[key].hasOwnProperty(i)) { continue; }

            var n = datetime[key][i];
            datetime[key][i] = (n < 10 ? '0' : '') + n; 
        }
    }

    return datetime.date.join('-') + 'T' 
         + datetime.time.join(':') + 'Z';
}
```

然后，它生成的时间戳可以传递给`new Date`，并在本地时间进行处理:

```
var timestamp = getTimestamp();

...

var rfcdate = new Date(timestamp).toString(); 
```** 

## **分享这篇文章****