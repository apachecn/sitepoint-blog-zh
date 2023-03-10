# 用 Zend 框架混合你的音乐

> 原文：<https://www.sitepoint.com/mash-up-music-zend-framework/>

在之前的一篇文章中，阿卡什·梅塔详细介绍了如何使用雅虎！Maps API 创建你自己的混搭。在这篇文章中，我们将看看使用其他一些雅虎！API 来创建我们自己的音乐混搭。同时，我将向您介绍 [Zend Framework](http://framework.zend.com/) ，这是一个节省时间、组织良好的开源框架，用于在 PHP 5 中开发 web 应用程序。

为了构建演示，我们将使用 [Audioscrobbler API](http://www.audioscrobbler.com/) (来自[last . FM](http://www.last.fm/))[Flickr API](http://www.flickr.com/)和[雅虎新闻](http://news.yahoo.com/)。

我们将构建的演示应用程序将提示用户输入他们最喜欢的乐队或艺术家。混搭将:

*   找到该艺术家的顶级专辑和曲目的链接。
*   显示相关艺术家。
*   显示来自 Flickr 的艺术家图片。
*   显示来自 Yahoo！新闻。

最终应用程序的截图如下。你可以在[这个现场演示](http://razorstone.com/sitepoint/musicmashup/)中亲自尝试一下，如果你想在家一起玩，可以随时[下载代码](https://www.sitepoint.com/examples/mashup/musicmashup.zip)。

![The finished mashup](img/820c9cd27fdffd3c90ce25ff09b28a5b.png)

##### 目标

本文不会对 Zend 框架的每一个类和方法进行全面深入的回顾，也不会让你从我们将要构建的演示混搭中获益(抱歉！).相反，它旨在激起您对 Zend 框架所能做的事情的兴趣。使用该框架，从外部来源获取数据非常容易，我希望您能以这篇文章为起点，探索该框架的其他方面，并将其集成到您的日常 PHP 代码中。

我不会解释演示应用程序的每一部分——只是那些演示 Zend 框架各种特性的代码部分。然而，如果您有兴趣更深入地研究它的工作方式，这段代码是很好的注释。

##### 你需要什么？

本文假设读者具备 PHP5 的工作知识。要在家里玩，你需要一个安装了 PHP 的 web 服务器(至少 5.1.4 版)。对于演示，我假设您使用的是 Apache。您还需要:

*   一个雅虎账户
*   一个[雅虎应用 ID](https://developer.yahoo.com/wsregapp/index.php)
*   来自 Flickr 的一个 [API 密钥](http://www.flickr.com/services/api/keys/apply/)
*   Zend 框架的副本

##### Zend 框架

Zend 框架是来自 [Zend](http://www.zend.com/) (参与创建 [PHP](http://www.php.net/) 的公司)的开源组件库。该框架背后的想法是为开发人员提供他们每天使用的大部分功能。引用[网站](http://framework.zend.com/whyzf)的话:

Zend Framework 延续了 PHP 的艺术&精神，它基于简单性、面向对象的最佳实践、企业友好的许可和经过严格测试的敏捷代码库……一个轻量级、松散耦合的组件库，简化为提供每个人所需的 4/5 的功能。

***Zend 框架亮点***

下面是这个框架能做什么的一个快照(我真的只是略述皮毛):

*   [模型-视图-控制器组件](http://en.wikipedia.org/wiki/Model-view-controller)用于分离设计和开发
*   数据库抽象允许统一访问多个数据库，如 MySQL、Oracle、Microsoft SQL Server、IBM DB2、PostgreSQL 和 SQLite
*   货币、日期、语言、时间、日历和度量的国际化和本地化
*   认证、授权和会话管理
*   内置对 web 服务的支持，如 [Akismet](http://akismet.com/development/api/) 、 [Amazon](http://www.amazon.com/b/104-3424087-1285559?%5Fencoding=UTF8&node=3435361) 、[audioscopbler](http://www.last.fm/feeds/)、 [Delicious](http://del.icio.us/help/api/) 、 [Flickr](http://www.flickr.com/services/api/) 、 [Simpy](http://www.simpy.com/doc/api/rest) 、 [StrikeIron](http://strikeiron.com/developers/default.aspx) 和 [Yahoo！](http://developer.yahoo.com)
*   SOAP，Feeds，Google 数据
*   邮件、搜索、验证、缓存等等

Rails，吃你的心吧！

***谁在使用框架？***

Zend 框架正在为一些大型 PHP 应用程序提供动力，举三个例子来说，比如 IBM 的 T2 QEDWiki 项目，Magento 和 Right Media。这应该给你一个框架采用的指示——如果你决定花时间学习 Zend 框架，这是一项可能在未来对你有好处的技能。

***入门***

将 Zend 框架的文件夹和文件解压到您的本地 web 服务器。你要的文件夹是:`Zend Framework-<version number>/library/Zend`。这包含了所有的类。

**设置应用路径**

在使用框架之前，我们需要指定 Zend 文件夹(包含所有类的文件夹)的[包含路径。有几种方法可以做到这一点——在`.htaccess`文件中，在`php.ini`文件中，或者从页面本身:](http://www.php.net/manual/en/ini.core.php#ini.include-path)

**1。**要通过您的 [`.htaccess`文件](http://en.wikipedia.org/wiki/.htaccess)添加包含路径，请在您服务器的 web 根文件夹中的`.htaccess`文件底部添加以下行(注意，您使用的值将取决于您放置文件的位置):

```
php_value include_path "/home/sitepoint/public_html/"
```

**2。**要在您的 [`php.ini`文件](http://au2.php.net/manual/en/ini.php)中进行更改，添加以下行:

```
include_path "/home/sitepoint/public_html/"
```

**3。**最后，从页面本身添加包含路径，使用 [`set_include_path()`](http://au.php.net/manual/en/function.set-include-path.php) 函数。这里有一个例子:

```
<?php 

if(set_include_path('/home/sitepoint/public_html/') === false){ 

  die('Include path failed'); 

} 

?>
```

注意，不能只使用 [`include()`](http://au.php.net/manual/en/function.include.php) 或者 [`required()`](http://au.php.net/manual/en/function.require.php) 来实现框架——这样做会导致致命错误。

***装载机类***

现在我们已经准备好使用框架了，让我们加载一些类。loader 类是向应用程序动态添加其他文件和类的一个很好的快捷方式。不一定非要使用它，因为一旦设置了 [`include_path`](http://www.php.net/manual/en/ini.core.php#ini.include-path) ，就可以使用 [`require_once()`](http://au.php.net/manual/en/function.require-once.php) 或 [`include_once()`](http://au.php.net/manual/en/function.include-once.php) 函数来达到同样的目的。

然而，在装入类的方式方面，loader 类为您提供了一些额外的灵活性。使用该类有两种主要方式，您可以用它来加载文件或加载类。

在这个例子中，我们将如下使用类装入器:

```
<?php 

// include the loader  

require_once ('Zend/loader.php'); 

// Load 'Zend_Filter_Input' class i.e. Zend/Filter/Input.php 

Zend_Loader::loadClass('Zend_Filter_Input'); 

?>
```

***过滤和验证***

`Zend_Filter_Input`组件实际上是一个数据保存区——一个只允许根据请求者的要求发布数据的区域。这有点像机场安检——只有持有有效证件和行李的人才被允许登机。人是数据，行李检查是过滤器，护照检查是验证器。因此，任何危险物品都会在行李安检时被移除(过滤)，只有持有正确护照和机票的人才能通过护照和机票控制(验证)登机。

过滤和验证类 [`Zend_Filter_Input`](http://framework.zend.com/manual/en/zend.filter.input.html) 是一个强大的组件，它不仅可以简化您编写的代码，还可以在过滤和验证数据的繁重(但却是必要的)任务上节省您大量的时间和精力。有益的是，数据在返回时也被转义为 HTML 输出，这应该立即转化为更少的 [XSS](http://en.wikipedia.org/wiki/Xss) 的担忧。

**可用的过滤器和验证器**

有 12 个标准的过滤器/验证类，每个类都以一个字符串作为参数:

*   `Alnum`–删除所有非字母或数字字符
*   `Alpha`–删除所有非字母字符
*   `BaseName`–返回文件的基本名称
*   `Digits`–删除所有非数字值
*   `Dir`–返回路径的目录名
*   `HtmlEntities`–将字符转换成它们的 HTML 等价物(如果存在的话)
*   `Int`–返回一个整数
*   `RealPath`–将相对路径，即`../../somefile.php`转换为绝对路径名，即`/root/home/somefile.php`
*   `StringToLower`–将字母字符转换成小写
*   `StringToUpper`–将字母字符转换成大写
*   `StringTrim`–从字符串的开头和结尾去除字符
*   `StripTags`–去除那些特别允许的 HTML 和 PHP 标签，以及那些允许的标签的属性；它还可以删除或允许评论，即`<!-- some HTML comment -->`

对于我们的演示应用程序，我们将使用`StripTags`和`StringTrim`。关于其他过滤器的更多信息，请参考 [Zend 框架的手册](http://framework.zend.com/manual/en/zend.filter.html)。

**声明过滤器和验证器规则**

为了使用 filter 输入类，我们需要将过滤器和验证器声明为数组。让我们来看看演示应用程序的第一段代码:

```
<?php  

$filters = array(  

  'artist' => array(  

    'StripTags',  

    'StringTrim'  

  )  

);  

$validators = array(  

  'artist'  => array(  

    'Alnum',  

  )  

);  

?>
```

在上面的例子中，我们在字段 artist 上使用了一个过滤器来移除标签并修剪字符串两端的空白。我们还为 artist 字段创建了一个验证器，检查它是否只包含字母或数字字符。

过滤器和验证器可以一次应用一个，就像我们在这里做的那样，但是也可以将它们链接在一起…

**链接**

很抱歉让你失望了——我们在这里没有谈论任何古怪的事情。链接是指一个接一个地执行过滤器和/或验证器，每个都使用前一个过滤器的输出值。这种功能实际上是默认发生的。但是，正如我们稍后将看到的，当您执行验证时，它可以被关闭。在上面的代码中，任何 HTML 标记或 PHP 代码都从字段 artist 中删除，然后删除周围的空白。

例如，字符串`<strong> Hello World </strong>`将被转换为“Hello World”。

**为过滤器和验证器创建处理器**

一旦我们声明了过滤器和验证器，下一步就是使用它们来处理我们的数据。在演示中，我们利用超级全局变量 [`$_GET`](http://php.net/manual/en/reserved.variables.php) 来检索所有的[查询字符串](http://en.wikipedia.org/wiki/Querystring)输入，就像这样:

```
$input = new Zend_Filter_Input($Filters, $Validators, $_GET);
```

**获取无效、缺失或未知字段**

该框架为我们提供了三种检查无效、缺失或未知数据的方法。每个方法都返回一个布尔值:

*   `hasInvalid`–如果数据未通过验证检查，则返回 true
*   `hasMissing`–指定由元命令`'presence' => 'required'`呈现的数据丢失(稍后将详细介绍)。)
*   `hasUnknown`–未指定验证但包含在输入数据中的字段

在我们的演示应用程序中，我们将使用`hasInvalid`和`hasMissing`作为简单的检查，检查我们的请求至少返回了一些有用的东西。然后，我们可以使用以下四种方法中的任何一种来获得关于错误数据的进一步信息:

*   `getInvalid`–获取任何无效数据的信息。
*   `getMissing`–获取任何缺失数据的信息。
*   `getUnknown`–获取任何意外数据的信息。
*   `getMessages`–获取任何无效和缺失数据的信息。

这些方法中的每一个都以此格式返回一个数组:

```
Array  

(  

    [field] => Array  

        (  

            [0] => Error message  

        )  

)
```

为了简单起见，我们将使用`getMessages`打印出任何错误消息:

```
<?php  

if ($input->hasInvalid() || $input->hasMissing()) {  

  print_r($input->getMessages());  

}  

?>
```

**获取我们的数据**

现在我们已经检查了数据，是时候检索它了。出于演示的目的，我们将使用 magic 访问器来检索字段 artist 的 HTML 转义输出:

```
echo $input->artist;
```

还可以使用`getEscaped`和`getUnescaped`方法分别检索已转义或未转义的数据。

使用 [Zend_Filter_Input 组件](http://framework.zend.com/manual/en/zend.filter.input.html)可以做更多的事情来控制过滤器和验证器，包括使用元命令和创建自己的过滤器。要了解更多，请查看[手册](http://framework.zend.com/manual/en/zend.filter.input.html)。

接下来，我们将看看 web 服务以及如何实现它们。

 *Zend_Service_Audioscrobbler 组件提供了一种简单的方法来访问来自[Audioscrobbler.com](http://www.audioscrobbler.com/)(last . FM)的 REST web 服务。这项服务使我们能够:

*   用户
*   艺术家
*   相册
*   轨道
*   标签
*   组
*   论坛

对于我们的演示应用程序，我们将检索给定艺术家的前十张专辑，并显示它们与[audioscopbler](http://www.audioscrobbler.com/)的链接。

首先，我们需要加载并实例化`Zend_Service_Audioscrobbler`类:

```
Zend_Loader::loadClass('Zend_Service_Audioscrobbler');  

$Audioscrobbler = new Zend_Service_Audioscrobbler();
```

接下来，我们指定想要检索信息的艺术家，在本例中是滚石乐队。

不要忘记 [`urlencode()`](http://au2.php.net/manual/en/function.urlencode.php) 这个名字，因为 Audioscrobbler 在它创建的 XML 数据文件的 URL 中使用艺术家的名字。

让我们指定艺术家:

```
$Audioscrobbler->set('artist', urlencode('The Rolling Stones'));  

$results = $Audioscrobbler->artistGetTopAlbums());
```

现在我们有了一些数据，我们将遍历结果集并打印专辑名称，将其链接回其在[Audioscrobbler.com](http://www.audioscrobbler.com/)上的页面:

```
foreach ($results as $info) {  

  echo '<a href="' . $info->url . '">' . $info->name. '</a><br />';   

}
```

这就是全部了。简单吧。

***Flickr***

Flickr web 服务遵循与 Audioscrobbler 非常相似的模式。但是，还有一些事情需要考虑，包括:

*   图像可用的许可证
*   归功于图像所有者

**授权图像**

在 Flickr 上提供图片有六种不同的许可。“版权所有”的图片不应在另一个网站上重新发布，除非你是所有者，或者你有所有者的许可。许可的其余部分是知识共享许可的变体，并规定了该许可所涵盖的每个图像可接受的特定用法:

*   0 =保留所有权利
*   1 = [归属非商业类股票许可](http://creativecommons.org/licenses/by-nc-sa/2.0/)
*   2 = [归属非商业许可](http://creativecommons.org/licenses/by-nc/2.0/)
*   3 = [归属非商业无衍生品许可](http://creativecommons.org/licenses/by-nc-nd/2.0/)
*   4 = [归属许可](http://creativecommons.org/licenses/by/2.0/)
*   5 = [归属同股同权](http://creativecommons.org/licenses/by-sa/2.0/)
*   6 = [归属无衍生品许可](http://creativecommons.org/licenses/by-nd/2.0/)

**把它们装起来**

同样，我们首先加载适当的类，并用 API 键实例化它:

```
Zend_Loader::loadClass('Zend_Service_Flickr');  

$flickr = new Zend_Service_Flickr('*<- YOUR API KEY HERE ->*');
```

**搜索图像**

Flickr 提供了通过标签或用户搜索图片的能力。对于我们的演示，我们将使用艺术家的名字作为我们的搜索标签。我们还将:

*   将结果限制在每页八张图片
*   仅显示结果的第一页
*   仅使用许可证 1、2、3、4、5 或 6 许可的图像(见上文)

以下是我们如何在 Flickr 上搜索那些持续流行的经典摇滚歌手:

```
$options = array(  

  'per_page' => 8,   

  'page'=> 1,   

  'license' => '1, 2, 3, 4, 5, 6'  

 );  

$results = $flickr->tagSearch('The Rolling Stones', $options);
```

还有进一步的选项来优化您的搜索——参见[文档](http://framework.zend.com/manual/en/zend.service.flickr.html)了解更多细节。

现在我们(希望)从我们的图像搜索中得到一些结果。您可以使用以下任何功能检查返回结果的数量:

*   `Zend_Service_Flickr_ResultSet::totalResultsAvailable`–返回当前搜索词可用的图片总数
*   `Zend_Service_Flickr_ResultSet::totalResultsReturned`–返回上次搜索返回的图像总数
*   `Zend_Service_Flickr_ResultSet::firstResultPosition`–返回最近返回的图像集相对于整个结果集的位置
*   `Zend_Service_Flickr_ResultSet::totalResults()`–返回结果集中图像的总数

每个结果图像都有可用的信息，包括图像 ID、图像所有者的 ID、图像拍摄日期、图像是否公开等等。您还可以访问多达六个不同版本的图像:

*   `Square`–75×75 像素缩略图
*   `Thumbnail`–100 像素缩略图
*   `Small`–图像的 240 像素版本
*   `Medium`–500 像素版本的图像
*   `Large`–640 像素版本的图像
*   `Original`–原始图像

此外，对于图像的每个版本，您可以访问四个属性:

*   `uri`–原始图像的 URI
*   `clickUri`–链接到图片的 Flickr 页面
*   `width`–图像的宽度，以像素为单位
*   `height`–图像的高度，以像素为单位

要了解更多关于图像细节的信息，请阅读 Zend_Service_Flickr 文档。

理论已经足够了——让我们在 mashup 中添加一些 Flickr 照片吧！

以下代码示例将遍历搜索结果，如果有多个图像可用(并且是公开的)，则显示该图像的方形缩略图，并带有指向 Flickr 上原始图像的链接:

```
$results = $Flickr->tagSearch($Artist, $FlickrOptions);  

if($results->totalResultsAvailable > 0) {  

  foreach ($results as $result){  

    if (isset($result->Square) && $result->ispublic =='1') {  

      $FlickResults .= '<a href="http://www.flickr.com/photos/'  

             + $result->owner.'/'.$result->id.'/"><img src="'  

             + $result->Square->uri.'" alt="'  

             + $HTMLFilter->filter($result->title).'  

             + " title="Picture from: '  

             + $HTMLFilter->filter($result->ownername)  

             +' at Flickr" /></a>'."n";  

    }  

  }  

}   

else {  

  // no results found  

  $FlickResults .= 'No Images Found';  

}
```

请注意我们是如何获得单个图像的属性的(在本例中是`clickUri`):

```
 $result->Square->clickUri
```

如果我们想显示完整的原始图像，我们可以使用:

```
 $result->Original->clickUri
```

这是在 Flickr 上搜索和访问图片信息的一种快速而肮脏的方式。接下来，我们将看看搜索雅虎新闻。

***雅虎新闻***

对于我们的演示应用程序，我们将只搜索相关的新闻故事，但这可以很容易地扩展到包括:

*   搜索网页
*   搜索图像
*   搜索本地企业和服务

与我们之前的 web 服务一样，我们加载 web 服务类——在本例中是`Zend_Service_Yahoo`——然后使用我们的 Yahoo 应用程序 ID 实例化该类。

```
Zend_Loader::loadClass('Zend_Service_Yahoo');   

$Yahoo = new Zend_Service_Yahoo('*<- Your Yahoo! App ID ->*');
```

**设置选项**

你可以使用一堆选项来优化你的新闻搜索，尽管大多数选项都有默认值。有关完整列表，请访问雅虎开发者网络文档。

在我们的 mashup 应用程序中，我们使用以下选项:

*   仅返回三个结果
*   首先显示第一个结果
*   按等级(即相关性)对我们的新闻项目进行排序
*   用英语显示结果
*   要求结果包含列出的所有查询词
*   仅显示 MTV.com 的结果

下面是这些选项在 PHP 代码中的表示方式:

```
$newOptions = array(   

'results' => 3,    

        'start' => 1,    

        'sort' => 'rank',    

        'language' => 'en',    

        'type' => 'all',   

        'site' => 'mtv.com'   

       );
```

要用这些选项向 Yahoo 发送查询，我们只需调用`newsSearch`函数，传递选项数组和我们的查询词:

```
$YahooResults = $Yahoo->newsSearch('The Rolling Stones', $newsOptions);
```

当我们的查询返回时，我们可以通过检查返回的对象的许多属性来检查收到的结果:

*   `totalResultsAvailable`–可搜索的结果总数
*   `ResultSettotalResultsReturned`–返回的结果总数
*   `firstResultPosition`–返回的新闻条目相对于完整结果集的位置
*   `totalResults()`–结果集中的项目总数

返回的每个新闻项目都具有以下属性:

*   `Title`–新闻标题
*   `Url`–新闻条目的 URL
*   `ClickUrl`–链接到新闻结果项的 URL
*   `Summary`–新闻结果汇总
*   分发文章的公司
*   `NewsSourceUrl`–物品分销公司的网址
*   `Language`–文章所用的语言
*   `PublishDate`–文章发表的日期(UNIX 时间戳)
*   `ModificationDate`–文章的最后修改日期(UNIX 时间戳)
*   `Thumbnail`–文章的缩略图，如果有的话

在我们的演示应用程序中，我们将:

1.  检查搜索结果

3.  遍历结果

5.  显示链接到文章的标题

代码如下:

```
$results = $Yahoo->newsSearch($Artist, $NewsOptions);   

if($results->totalResultsReturned > 0){   

  foreach ($results as $result) {   

    $YahooNewsResults.= '<strong><a href="'.$result->ClickUrl   

    + '">'.$HTMLFilter->filter($result->Title)   

    + '</a></strong>'."n";   

    $YahooNewsResults.= '<div class="newscredit">Posted by  '   

    + $result->NewsSource. ' on '.date('D jS M Y', $result->PublishDate)   

    + '</div>'."n";   

    $YahooNewsResults.= $HTMLFilter->filter($result->Summary)    

       + '<br /><br />'."n";   

  }   

}   

else {   

  $YahooNewsResults.= 'No News Items Found';   

}
```

有关访问雅虎网络服务的更多信息，请参考 [Zend 框架文档](http://framework.zend.com/manual/en/zend.service.yahoo.html)。

 *在本文中，我们探索了 Zend 框架的几个方面——希望从您所看到的内容中，您能够体会到它是一个多么强大和简单的框架。

在这个过程中，我们查看了一些您将用来构建音乐混搭应用程序的代码——如果您想在家里玩这个应用程序，不要忘记[下载所有的源代码](https://www.sitepoint.com/examples/mashup/musicmashup.zip)。[的现场演示](http://razorstone.com/sitepoint/musicmashup/)远不是一个完美的、可投入生产的应用程序，但它是一个很好的起点，你可以从这里开始尝试自己的想法。这段代码经过了很好的注释，应该会为您创建自己的 mashups 打下坚实的基础，并尝试使用 Zend 框架。尽情享受吧！* 

## *分享这篇文章**