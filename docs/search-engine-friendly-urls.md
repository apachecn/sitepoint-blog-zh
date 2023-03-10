# 搜索引擎友好的 URL

> 原文：<https://www.sitepoint.com/search-engine-friendly-urls/>

在今天的互联网上，数据库驱动或动态网站非常流行。不幸的是，在页面之间传递信息最简单的方法是使用查询字符串。如果你不知道什么是查询字符串，它是一串附加在 URL 末尾问号后面的信息。

那么，这有什么问题呢？大多数搜索引擎(除了少数例外——即谷歌)不会索引任何在 URL 中有问号或其他字符(如&符号或等号)的页面。所以所有那些流行的动态网站都没有被编入索引——如果没有人能找到它，那么一个网站还有什么用呢？

解决办法？搜索引擎友好的网址。有一些流行的方法可以在不使用查询字符串的情况下将信息传递到您的页面，这样搜索引擎仍然会对这些单独的页面进行索引。我将在本文中介绍其中的 3 种技术。这三个版本都可以在 PHP 中与 Linux 上的 Apache 一起工作(虽然它们可以在其他场景中工作，但我不能确定它们是否可以)。

##### 方法 1:路径信息

***实现:***

如果你在这篇文章上方的地址栏中查看，你会看到这样一个 URL:http://www.webmasterbase.com/article.php/999/12.站点实际上使用 PATH_INFO 方法来创建他们的动态页面。

Apache 有一个“向后看”的特性，如果没有找到想要的东西，它会沿着 URL 向后扫描。在这种情况下，没有名为“12”的目录或文件，所以它查找“999”。但是它发现也没有名为“999”的目录或文件，所以 Apache 继续向下查找 URL 并看到“article.php”。这个文件确实存在，所以 Apache 调用这个脚本。Apache 还有一个名为`$PATH_INFO`的全局变量，它是在每次 HTTP 请求时创建的。这个变量包含的是被调用的脚本，以及 URL 中该信息右边的所有内容。所以在我们一直使用的例子中，`$PATH_INFO`将包含`article.php/999/12`。

所以，你想知道，我如何使用`article.php/999/12`查询我的数据库？首先你要把它分解成你能用的变量。您可以使用 PHP 的`explode`函数来实现:

```
$var_array = explode("/",$PATH_INFO);
```

完成后，您将获得以下信息:

```
$var_array[0] = "article.php"
```

```
$var_array[1] = 999
```

```
$var_array[2] = 12
```

所以你可以用`rename $var_array[1]`作为`$article` ，用`$var_array[2]`作为`$page_num` 来查询你的数据库。

***缺点:***

这种方法以前有一个主要缺点。谷歌，也许还有其他搜索引擎，不会索引以这种方式建立的页面，因为他们认为 URL 格式不正确。我联系了谷歌的一名软件开发人员，让他们意识到了这个问题，我很高兴地宣布这个问题现在已经解决了。

其他搜索引擎可能会忽略以这种方式设置的页面。虽然我不知道有，但我不能确定没有。如果您决定使用这种方法，一定要监视服务器日志中的蜘蛛，以确保您的站点被索引。

##### 方法二:。htaccess 错误页面

***实现:***

第二种方法涉及使用。htaccess 文件。如果你是新手。htaccess 是一个用于管理 Apache 访问选项的文件，无论您将它放在哪个目录中。服务器管理员有一个更好的方法，使用他或她的配置文件，但是由于我们大多数人没有自己的服务器，我们无法控制服务器管理员做什么。现在，服务器管理员可以配置用户可以对其执行的操作。htaccess 文件，所以这种方法可能无法在您的特定服务器上工作，但是在大多数情况下可以。如果没有，您应该联系您的服务器管理员。

这种方法利用了。htaccess 处理错误的能力。在。htaccess 文件，只需插入下面一行:

```
ErrorDocument 404 /processor.php
```

现在创建一个名为`processor.php`的脚本，放在同一个目录下，就完成了！假设您有以下 URL:http://www.domain.com/directory/999/12/.，在本例中“999”和“12”也不存在，但是，由于您没有在目录路径的任何地方指定脚本，Apache 将创建一个 404 错误。Apache 没有将通用的 404 头发送回浏览器，而是在。htaccess 文件并调用 processor.php。

现在，在第一个例子中，我们使用了`$PATH_INFO`变量，但是这次不行了。相反，我们需要使用`$REQUEST_URI`变量，它包含 URL 中域之后的所有内容。所以在这种情况下，它包含:/directory/999/12/。

在 processor.php，您需要做的第一件事是发送一个新的 HTTP 头。记住，Apache 认为这是一个 404 错误，所以它想告诉浏览器它找不到页面。

因此，在您的 processor.php 中输入以下代码行:

```
header("HTTP/1.1 200 OK");
```

这时我需要指出一个重要的事实。在第一个例子中，您可以指定哪个脚本处理您的 URL。在这个例子中，所有的 URL 都必须由同一个脚本 processor.php 来处理，这就有点不同了。而不是创建不同的网址的基础上，你想做什么，如 article.php/999/12 或 printarticle.php/999/12，你只有一个脚本，必须做到这两个。

因此，您必须根据 processor.php 收到的信息来决定要做什么——更具体地说，是通过计算传递了多少个参数。例如在[我的网站](http://www.online-literature.com)上，我使用这种方法生成我的页面:我知道如果只有一个参数，比如 http://www.online-literature.com/shakespeare/,，我需要加载一个作者信息页面；如果有 2 个参数，比如 http://www.online-literature.com/shakespeare/hamlet/,我知道我需要加载一个图书信息页面；最后，如果有 3 个参数，如 http://www.online-literature.com/shakespeare/hamlet/3/,，我知道我需要加载一个章节查看页面。或者，您可以简单地使用第一个参数来指示要显示的页面类型，然后基于该参数处理其余的参数。

有两种方法可以完成计算参数的任务。首先你需要使用 PHP 的`explode`函数来分解`$REQUEST_URI` 变量。所以如果`$REQUEST_URI = /shakespeare/hamlet/3/`:

```
$var_array = explode("/",$REQUEST_URI);
```

现在注意，由于/'的位置，这个数组中实际上有 5 个元素。第一个元素(元素 0)为空，因为它包含第一个/之前的信息。第五个元素(元素 4)也是空的，因为它包含最后一个/之后的信息。

所以现在我们需要计算`$var_array`中的元素。PHP 有两个函数让我们做到这一点。我们可以使用`sizeof()`函数，如下例所示:

```
$num = sizeof($var_array); // 5
```

您会注意到,`sizeof()`函数计算数组中的每一项，不管它是否为空。另一个函数是`count()`，它是`sizeof()`函数的别名。

一些搜索引擎，比如 AOL，会自动从你的 URL 中删除尾随的/例如，http://www.online-literature.com/shakespeare/hamlet/变成了 http://www.online-literature.com/shakespeare/hamlet,，由于该数组中总共有 3 个元素，我们的 processor.php 将加载一个作者页面而不是图书页面。

解决方案是创建一个函数，只计算数组中实际保存数据的元素。这将允许你省去结尾和/或允许任何来自 AOL 搜索引擎的链接指向正确的位置。这种功能的一个例子是:

```
function count_all($arg)  

{   

  // skip if argument is empty   

  if ($arg) {   

    // not an array, return 1 (base case)   

    if(!is_array($arg))   

    return 1;   

    // else call recursively for all elements $arg   

    foreach($arg as $key => $val)   

    $count += count_all($val);   

    return $count;   

  }   

}
```

要获得您的计数，请像这样访问函数:

```
$num = count_all($url_array);
```

一旦知道需要多少参数，就可以这样定义它们:

```
$author=$var_array[1]; 

$book=$var_array[2];

$chapter=$var_array[3];
```

然后，您可以使用 includes 调用适当的脚本，该脚本将查询您的数据库并设置您的页面。此外，如果您得到了您不期望的结果，您可以简单地创建自己的错误页面以显示给浏览器。

***缺点:***

这种方法的缺点是，Apache 会将命中的每个页面视为错误。因此，每次点击都会在您的服务器错误日志中创建另一个条目，这实际上破坏了它们的有用性。所以如果你使用这种方法，你就牺牲了你的错误日志。

##### 方法 ForceType 指令

***实现:***

你可能还记得，当使用`PATH_INFO`方法时，会让谷歌甚至其他搜索引擎出错的是 URL 中间的句点。那么，如果有一种不用句号的方法呢？你猜怎么着？有！它是使用 Apache 的 ForceType 指令实现的。

ForceType 指令允许您覆盖已经设置的任何默认 MIME 类型。通常它可以用来将一个 HTML 页面解析为 PHP 或类似的东西，但是在这种情况下，我们将使用它来解析一个没有 PHP 扩展名的文件。

因此，不要像我们在方法 1 中那样使用 article.php，而是将该文件重命名为“article”。然后，您将能够像这样访问它:http://www.domain.com/article/999/12/,利用 Apache 的回看特性和 PATH_INFO 变量，如方法 1 中所述。但是现在，Apache 不知道“文章”需要被解析为 php。要告诉它这一点，您必须将以下内容添加到您的。htaccess 文件。

```
<Files article>    

  ForceType application/x-httpd-php    

</Files>
```

这就是所谓的“容器”。Apache 不是将指令应用于所有文件，而是允许您通过文件名、位置或目录来限制它们。您需要如上创建一个容器，并将指令放入其中。在本例中，我们使用了一个文件容器，我们将“article”标识为我们所关心的文件，然后在关闭容器之前，列出我们希望应用于该文件的指令。

通过将指令放在容器中，我们告诉 Apache 将“`article`”解析为 PHP 脚本，即使它没有文件扩展名。这允许我们去掉 URL 中导致问题的句点，但仍然使用 PATH_INFO 方法来管理我们的站点。

***缺点:***

与方法 2 相比，这种方法唯一的缺点是你的 URL 会稍微长一点。例如，如果我要在我的网站上使用这种方法，我必须使用这样的 URL:http://www.online-literature.com/ol/homer/odyssey/而不是 http://www.online-literature.com/homer/odyssey/.，但是如果你有一个像 SitePoint 这样的网站并使用这种方法，就不会有这样的问题，因为 URL(http://www.SitePoint.com/article/755/12/)会更有意义。

##### 结论

我概述了 3 种制作搜索引擎友好 URL 的方法——以及它们的缺点。显然，在决定实现哪种方法之前，您应该评估这些缺点。如果你对这些技术的实现有任何问题，这些都是在 [SitePoint 论坛](http://www.sitepointforums.com)上经常讨论的话题，所以只要停下来发个帖子就可以了。

## 分享这篇文章