# 使用 Solarium 和 SOLR 进行搜索-设置

> 原文：<https://www.sitepoint.com/using-solarium-solr-search-setup/>

Apache 的 [SOLR](http://lucene.apache.org/solr/) 是一个基于 Apache Lucene 的企业级搜索平台。它提供了强大的全文搜索以及高级功能，如分面搜索、结果突出显示和地理空间搜索。它具有极强的可扩展性和容错性。

据说使用 SOLR 来增强搜索功能的知名网站包括 digg、网飞、Instagram 和 Whitehouse.gov。

虽然 SOLR 是用 Java 编写的，但它可以通过 HTTP 访问，这使得与您喜欢的任何编程语言集成成为可能。如果您正在使用 PHP，那么[Solaris 项目](http://www.solarium-project.org)使集成变得更加容易，提供了底层请求的抽象级别，使您能够使用 SOLR，就像它是在您的应用程序中运行的本机实现一样。

在这个系列中，我将同时介绍 SOLR 和日光浴室。我们将从安装和配置 SOLR 并创建搜索索引开始。然后，我们将看看如何索引文档。接下来，我们将实现一个基本的搜索，然后用一些更高级的功能扩展它，如分面搜索、结果突出显示和建议。

在此过程中，我们将构建一个简单的应用程序来搜索电影集。你可以在这里获得[源代码，或者在这里](https://github.com/lukaswhite/Movie-Search)看一个[在线演示。](http://movies.demos.lukaswhite.com)

## 基本概念和操作

在我们深入研究实现之前，有必要了解一些基本概念以及将会发生什么的总体情况。

SOLR 是一个 Java 应用程序，作为 web 服务运行，通常在一个 servlet 容器中，如 Tomcat、Glassfish 或 JBoss。您可以使用 XML、JSON、CSV 或二进制格式通过 HTTP 操作和查询它——因此您可以为您的应用程序使用任何编程语言。然而，Solarium 库提供了一个抽象级别，允许您调用方法，就像 SOLR 是本机实现一样。出于本教程的目的，我们将在与我们的应用程序相同的机器上运行 SOLR，但实际上它可能位于一个单独的服务器上。

SOLR 创建了一个包含**个文档**的搜索索引。这通常反映了我们在现实生活中对文档的看法；一篇文章、一篇博客甚至一整本书。然而，文档也可以表示适用于您的应用程序的任何对象——产品、地点、事件——或者在我们的示例应用程序中是一部电影。

最基本的，SOLR 允许你对文档进行全文搜索。想想搜索引擎；你通常会搜索关键词、短语或完整的标题。使用 SQL 的`LIKE`子句只能做到这一步；这就是全文搜索的用武之地。

您还可以将附加信息附加到索引搜索文档中，这些信息不一定会被基于文本的搜索所获取；例如，您可以将产品的价格、某处房产的房间数量或某个项目添加到数据库的日期包含在内。

多面是 SOLR 最有用的特征之一。如果你曾经在网上购物，你可能会见过面搜索；方面允许您通过应用“过滤器”来“向下钻取”搜索结果。例如，在搜索了一家网上书店后，您可以使用过滤器将搜索结果限制为特定作者、特定流派或特定格式的书籍。

SOLR 的一个实例运行在一个或多个内核上。核心是配置和索引的集合，每个配置和索引都有自己的模式。通常，单个实例特定于特定的应用程序。由于不同类型的内容可能具有非常不同的结构和信息，例如，考虑产品、文章和用户之间的差异，一个应用程序通常在一个 SOLR 实例中具有多个内核。

## 安装 SOLR

我将提供如何在 Mac 上设置 SOLR 的说明；对于其他操作系统，[查阅文档](http://lucene.apache.org/solr/documentation.html)——或者，你可以下载 [Blaze](http://blazeappliance.sourceforge.net/) ，一个预装了 SOLR 的设备。

在 Mac 上安装 SOLR 最简单的方法是使用[自制软件](http://brew.sh/):

```
brew update
brew install solr 
```

这将把软件安装在一个目录中，比如`/usr/local/Cellar/solr/4.5.0`，这取决于你使用的软件版本。

使用提供的 Java 归档文件(JAR)启动服务器:

```
cd /usr/local/Cellar/solr/4.5.0/libeexec/example
java -jar start.jar 
```

要验证安装是否成功，请尝试在 web 浏览器中访问管理界面:

```
http://localhost:8983/solr/ 
```

如果您看到左上角带有 Apache SOLR 徽标的管理仪表板，则服务器已经启动并正在运行。

提示:要停止 SOLR——无论何时更改配置都需要这样做，我们马上就要这么做了——只需按下`CTRL + C`。

(Linux 说明:[http://www . lullabot . com/blog/article/installing-Solr-use-Drupal](http://www.lullabot.com/blog/article/installing-solr-use-drupal))

## 设置模式

开始使用 SOLR 最简单的方法可能是复制默认目录，然后定制它。

从`libexec/example`复制`solr`目录；在这里，我们创建了一个名为“电影”的新 SOLR 核心:

```
cd /usr/local/Cellar/solr/4.5.0/libeexec/example
cp -R solr movies 
```

稍后我们将查看配置文件，`movies\solr.xml`和`movies\collection1\conf\solrconfig.xml`。目前，我们真正感兴趣的是模式，它定义了我们要索引的文档中的字段，以及如何处理它们。

定义这个的文件是`movies\collection1\conf\schema.xml`。

如果你打开你刚刚复制过来的那个，你会看到它不仅包含一些有用的默认设置，而且它还有广泛的注释来帮助你理解如何定制它。

模式配置文件负责两个主要方面；**字段**和**类型**。类型只是简单的数据类型，在本质上，它们将类型名(如整数、日期和字符串)映射到实现中使用的底层 Java 类。比如:`solr.TrieIntField`、`solr.TrieDateField`、`solr.TextField`。类型配置还定义了记号赋予器、分析器和过滤器的行为。

以下是一些基本类型的示例:

```
<fieldType name="string"    class="solr.StrField"  sortMissingLast="true" omitNorms="true" />
<fieldType name="long"      class="solr.TrieLongField" precisionStep="0" positionIncrementGap="0"/>
<fieldType name="int"       class="solr.TrieIntField" precisionStep="0" positionIncrementGap="0"/>
<fieldType name="tdouble" class="solr.TrieDoubleField" precisionStep="8" omitNorms="true" positionIncrementGap="0"/>
```

`string`类型值得仔细观察，因为这里有一个陷阱。当您将字段用作字符串时，任何数据都会按照您输入的内容进行存储。此外，为了让查询匹配它，它必须是相同的。例如，假设您有一个字符串形式的文章标题，并插入了一个标题为“SOLR 简介”的文档。在任何适当的搜索实现中，您都希望通过诸如“SOLR 简介”这样的查询找到文章，更不用说“Solr 简介”了。为了解决这个问题，如果您不想要这种精确匹配行为——这在某些情况下实际上是有用的，比如分面搜索，那么您可以使用标记化器和过滤器的组合。

记号赋予器将文本分割成块——通常是独立的单词。过滤器以某种方式转换文本。为了说明这一点，我们来看一个合理的文本默认设置:

```
 <fieldType name="text_general" class="solr.TextField" positionIncrementGap="100">
        <analyzer type="index">
        <tokenizer class="solr.StandardTokenizerFactory"/>
        <filter class="solr.StopFilterFactory" ignoreCase="true" words="stopwords.txt" />        
        <filter class="solr.LowerCaseFilterFactory"/>
      </analyzer>
      <analyzer type="query">
        <tokenizer class="solr.StandardTokenizerFactory"/>
        <filter class="solr.StopFilterFactory" ignoreCase="true" words="stopwords.txt" />
        <filter class="solr.SynonymFilterFactory" synonyms="synonyms.txt" ignoreCase="true" expand="true"/>
        <filter class="solr.LowerCaseFilterFactory"/>
      </analyzer>
    </fieldType>
```

首先，您会注意到我们正在定义索引时的行为——换句话说，当您添加文档时数据是如何转换的——以及查询时的行为。在这个例子中，`LowerCaseFilterFactory`将数据在索引和查询时都转换成小写，因此大写变得无关紧要，我们可以进行对等比较。在我们的例子中，“介绍”将匹配“介绍”，“SOLR”将匹配“Solr”。

`StopFilterFactory`用于剔除停用词，停用词是因为与搜索无关或为了提高效率而被排除在外的常用词，如“a”、“the”、“and”等。这里有一个很好的、详尽的停用词列表。在上面的代码中，停用词是在单独的文本文件中配置的。

`fields`部分用于定义可用字段、它们的类型和附加信息，例如它们是否有多个值、它们是否应该被索引等等。

我们不会尝试修改或扩展类型定义——这超出了本教程的范围——而是我们要看的是字段定义。

概括地说，有两种定义文档结构的方法。首先是显式定义所有可能的字段。第二种方法是使用动态字段，这使您能够在遵守某些命名约定的情况下动态添加字段。例如，给定以下动态字段定义:

```
<dynamicField name="*_s"  type="string"  indexed="true"  stored="true" /> 
```

…比方说，您可以在文档中添加一个名为`author_s`的单值属性，它将被存储为一个字符串，而无需预先配置。(注:“作者”和“s”部分是完全分开的，不要理解为复数的“作者”。)下面定义了一个多值字符串字段:

```
<dynamicField name="*_ss" type="string"  indexed="true"  stored="true" multiValued="true"/> 
```

…例如，您可以使用`categories_ss`来添加类别。

如果你试图添加一个文档到搜索索引中，而这个索引包含了没有明确定义的属性，或者，如果你使用了动态属性，并且没有遵守这些约定，那么 SOLR 将会产生一个错误。要改变这种行为，请找到并取消注释(或添加)以下行:

```
<dynamicField name="*" type="ignored" multiValued="true" /> 
```

这一行表示应该忽略以前没有定义的属性，而不是生成错误。因为它们被忽略了，但是，请注意，它们不会被索引或存储，所以它们不会对您可能运行的任何搜索产生任何影响。

出于本教程的目的，我们将明确定义我们想要的字段。

```
 <field name="id" type="string" indexed="true" stored="true" required="true" multiValued="false" />

    <field name="title" type="text_general" indexed="true" stored="true"/>
    <field name="synopsis" type="text_general" indexed="true" stored="true" omitNorms="true"/>
    <field name="rating" type="string" indexed="true" stored="true" />
    <field name="cast" type="text_general" indexed="true" stored="true" multiValued="true"/>
    <field name="year" type="int" indexed="true" stored="true" />
    <field name="runtime" type="int" indexed="true" stored="true" />
```

SOLR 要求使用以下代码行:

```
<field name="_version_" type="long" indexed="true" stored="true"/> 
```

下列字段未被使用；然而，因为在`solrconfig.xml`中有许多对它的引用，所以现在把它留在这里是个好主意:

```
<field name="text" type="text_general" indexed="true" stored="false" multiValued="true"/> 
```

我们还需要指定哪个字段是唯一标识符——想想 SQL 术语中的主键——如下所示:

```
<uniqueKey>id</uniqueKey> 
```

现在我们需要告诉 SOLR 使用哪种配置。如果服务器当前正在运行，停止它(CTRL+ C)，这次用`Dsolr.solr.home`标志运行它:

```
cd /usr/local/Cellar/solr/4.5.0/libeexec/example
java -jar start.jar -Dsolr.solr.home=movies 
```

## 摘要

这是第一部分，我们已经开始看 SOLR 和日光浴室。我们已经安装了 SOLR，并设置了一个模式。在下一部分中，我们将使用 Solarium 设置我们的应用程序，并索引一些数据。

## 分享这篇文章