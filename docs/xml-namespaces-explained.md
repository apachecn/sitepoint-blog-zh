# XML 名称空间解释

> 原文：<https://www.sitepoint.com/xml-namespaces-explained/>

 ****##### 为什么我们需要名称空间？** 

名称空间实际上有两个基本需求:

1.  为了消除碰巧具有相同名称的两个元素之间的歧义

3.  将与共同想法相关的元素组合在一起

好了，这些陈述有点模糊，让我们在这里举一些例子:

***消除碰巧同名的元素之间的歧义***

考虑一下:

*   在(x)html 中有一个`table`元素。XSL-FO 中还有一个同名的元素。
*   `a`、`title`和`style`都是(x)html 和 SVG 中的元素。

那么，如何区分 SVG `title`和(x)html 呢？

***将与共同想法有关的要素组合在一起***

在(x)html 中，`table`、`style`和`a`元素由特定的规则管理，这些规则关于什么是必需的，什么可以包含，什么不可以包含。这些规则所要求的定义都应放在同一个地方。

例如，我自己的基于 XML 的数据可能有验证规则，我希望:

*   在同一个地方定义这些规则，并且
*   将这些特定的规则与我(或其他人)定义的任何其他规则集区分开来。

##### 什么是名称空间？

名称空间是唯一的 URI(统一资源定位符)

这种格式的优点是，任何传输 XML 的人都可以被认为可以访问域名(http://之后、下一个/之前的位)。搭别人的顺风车是不好的行为(尤其是当他们不知道你在做的时候！).

在 XML 文档中，URI 与一个前缀相关联，这个前缀与每个元素一起使用，以指示元素属于哪个名称空间。举个例子，

```
rdf:description  

xsl:template  

zblsa:data 
```

在这些例子中，

*   冒号前的部分是*前缀*
*   冒号后的部分是*局部部分*
*   任何带前缀的元素都是一个*限定名*
*   任何无前缀的元素都是一个*非限定名*

##### 如何使用名称空间？

要使用命名空间，首先要将 URI 与命名空间相关联:

```
<foo:tag xmlns:foo="http://me.com/namespaces/foofoo">.
```

这将`foo`定义为该元素标签的名称空间的前缀。以`xmlns`为前缀的属性就像一个命令，说“将下列字母链接到一个 URI”。因为没有一个格式良好的文档可以包含两个相同的属性，所以出现在冒号后面的部分会阻止同一个前缀被同时定义两次。

***为一个名称空间定义一个前缀***

这里有一个例子，我们为一个名称空间定义了一个前缀:

```
<foo:tag xmlns:foo="http://me.com/namespaces/foofoo"> 

  <foo:head> 

    <foo:title>An example document</foo:title> 

  </foo:head> 

  <foo:body> 

    <foo:e1>a simple document</foo:e1> 

    <foo:e2> 

      Another element 

    </foo:e2> 

  </foo:body> 

</foo:tag> 
```

对于`<foo:tag>`中的所有元素，名称空间前缀`foo`与名称空间 URI `http://me.com/namespaces/foofoo`相关联。

***为同一个名称空间定义多个前缀***

不同的前缀可能实际上指的是同一个名称空间，如下所示:

```
<tag>  

  <foo:head xmlns:foo="http://me.com/namespaces/foofoo">  

    <foo:title>An example document</foo:title>  

  </foo:head>  

  <bar:body xmlns:bar="http://me.com/namespaces/foofoo">  

    <bar:e1>a simple document</bar:e1>  

    <bar:e2>  

      Another element  

    </bar:e2>  

  </bar:body>  

<tag>
```

***为多个名称空间定义相同的前缀***

根据上下文，同一个前缀也可能引用不同的名称空间(尽管不建议这样做):

```
<myns:html xmlns:myns="http://www.w3c.org/1999/xhtml">  

<myns:head>  

<myns:title>A really bad idea</myns:title>  

</myns:head>  

<myns:body>  

  <myns:h1>A really bad idea</myns:h1>  

    <myns:pre>  

      <myns:pre xmlns:myns="http://my.com/namespaces/test-data">  

        <myns:table>  

          <myns:data>  

            Hello World  

          </myns:data>  

        </myns:table>  

      </myns:pre>  

   </myns:pre>  

</myns:body>
```

*注意:这是**而不是**的好主意！*

##### 多个名称空间

如果您正在使用名称空间，您几乎肯定需要一次使用几个名称空间——那么如何一次声明多个名称空间呢？

你要做的是使用不止一个`xmlns`声明，就像这样:

```
<foo:tag xmlns:foo="http://me.com/namespaces/foofoo"  

         xmlns:bar="http://me.com/namespaces/foobar"  

         >  

  <foo:head>  

    <foo:title>An example document</foo:title>  

  </foo:head>  

  <bar:body>  

    <bar:e1>a simple document</bar:e1>  

    <bar:e2>  

      Another element  

    </bar:e2>  

  </bar:body>  

</foo:tag>
```

##### 默认命名空间

***问题:*** 如果使用任何名称空间，是不是所有元素都必须存在于一个名称空间内？

***回答:*** 可以，但这不一定是问题！

允许定义一个没有前缀的名称空间——它们是我们上面提到的非限定名称。

这对于 xhtml 尤其重要，因为这种语言的一个要求是 xhtml 不能破坏 html——而且 HTML 不能理解前缀！

要定义默认名称空间，只需分配一个不带前缀的`xmlns`:

```
<xhtml > 
```

例如:

```
<html   

      xmlns:bar="http://me.com/namespaces/foobar"  

      >  

  <head>  

    <title>An example document</title>  

  </head>  

  <body>  

    <bar:e1>a simple document</bar:e1>  

    <bar:e2>  

      Another element  

    </bar:e2>  

  </body>  

</html> 
```

##### 属性和名称空间

对于任何特定的元素，一个属性只能存在一次。这使得属性与元素略有不同。

属性可以放在特定的名称空间(`<.... myns:myattib="foo" ...>`)中，也可以不加限定。

属性的正常“规则”是，只有当特定的名称空间定义了所讨论的属性时，才把它们放在名称空间*中(比如 xlink 或 rdf 属性)。*

没有名称空间前缀的属性不由名称空间定义。请注意，这与在*默认的*名称空间中是不同的。

只有当您要求文档符合将属性定义为限定属性的 DTD 或模式时，将属性放在名称空间中才变得重要。

##### 我应该在命名空间 URI 的末尾放什么？

没什么！

好吧，所以这没什么帮助。这里的问题是，人类看到一个 URL，所以他们想把他们的网络浏览器指向它，看看他们得到什么。这纯粹是人的事情，是决定标准化名称空间的 URIs 的结果。

引用 Claude L. Bullard 的话(来自 XML-Dev 电子邮件列表):

这个缺陷是名字、位置和身份的混淆，但这个缺陷是万维网运行的基本特征，所以我们被困在那里。所有关于 URN/URI/URL 的手势都没有回避一个简单的事实:如果你把 http://放在浏览器显示空间的任何地方，系统会把它涂成蓝色并竖起一根手指。

猴子期待一个资源，当它得不到时，这让猴子震惊。猴子不会通过阅读说明书来找出它们不应该被电击的原因。他们变红了，竖起一根手指。

许多人避免这种“震惊”因素的方法是向错误的查看者发布一个描述名称空间的文档。建立在这种方法上的一个新想法，现在正从 XML-dev 中出现，就是 [RDDL](http://www.openhealth.org/RDDL) (资源目录描述语言)。

##### 进一步阅读

***主要 XML 门户***

*   [www.xml.com](http://www.xml.com)
*   [www.xml.org](http://www.xml.org)
*   [www.xml.net](http://www.xml.net)(未来)

***拥有 XML 信息的门户***

*   [www.w3c.org](http://www.w3c.org)
*   [www.oasis-open.org](http://www.oasis-open.org)
*   [xml.apache.org](http://xml.apache.org)

## 分享这篇文章**