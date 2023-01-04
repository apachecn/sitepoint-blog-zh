# Tomcat 烂透了…阿帕奇有缺陷吗？

> 原文：<https://www.sitepoint.com/tomcat-sucks-apache-flawed/>

在我的 Java 博客列表中名列前茅的是 Hani Suleiman 的 BileBlog，他在其中对流行的 Java 项目及其背后的人进行了毫不掩饰的尖刻评论。过去，他对 [Apache Project](http://www.apache.org/) 的各种开源 Java 产品不太满意，比如 [Maven](http://maven.apache.org/) 和 [Struts](http://struts.apache.org/) 。今天，他责备了 Tomcat。

当然，Tomcat 为 Servlet 和 JSP 规范提供了参考实现，但是由于它是免费的，所以它也是许多中小型企业的首选服务器。不久前，我[在寻找一个初学者友好的 Java web 应用服务器时，对 Tomcat](https://www.sitepoint.com/in-search-of-a-beginner-friendly-server/) 进行了批判性的审视(我仍在寻找)。

苏莱曼对 Tomcat 的批评是基于项目代码的质量——或者更确切地说是缺乏质量——这有点令人耳目一新(他经常对开发人员进行没有说服力的、甚至是有趣的人身攻击)。他选择了一个关键但相对不复杂的类( [`DefaultServlet`](http://svn.apache.org/repos/asf/tomcat/container/tc5.5.x/catalina/src/share/org/apache/catalina/servlets/DefaultServlet.java) ，负责提供 HTML/CSS/JavaScript 文件和图像等静态资源)，指出了许多糟糕的编码实践的例子，这些只是其中的一部分:

Initialization code that lazily catches `Throwable`

```
 // Set our properties from the initialization parameters
        String value = null;
        try {
            value = getServletConfig().getInitParameter("debug");
            debug = Integer.parseInt(value);
        } catch (Throwable t) {
            ;
        }
        try {
            value = getServletConfig().getInitParameter("input");
            input = Integer.parseInt(value);
        } catch (Throwable t) {
            ;
        }
```

```
Throwable is the base interface for all Java exceptions an errors, many of which have no business being caught by this sort of code, let alone being ignored as this code will do. If the server happens to run out of memory while initializing a DefaultServlet, for example, this code will ignore the resulting error and attempt to continue running.
```

Attempts to identify particular exceptions by portions of their message strings

```
 try {
            serveResource(request, response, true);
        } catch( IOException ex ) {
            // we probably have this check somewhere else too.
            if( ex.getMessage() != null
                && ex.getMessage().indexOf("Broken pipe") >= 0 ) {
                // ignore it.
            }
            throw ex;
        }
```

幸运的是，有问题的`if`语句(从评论来看，开发人员似乎对此相当不确定)实际上并没有导致它所识别的异常被忽略(正如第二条评论所说的那样)，所以这种糟糕编程的混乱扭曲实际上是无用的。

A method that returns an exception instead of throwing it

```
 protected IOException copyRange(InputStream istream,
                                  ServletOutputStream ostream) {

        // Copy the input stream to the output stream
        IOException exception = null;
        ...
            try {
                ...
            } catch (IOException e) {
                exception = e;
                ...
            }
        ...
        return exception;

    }
```

作为一个开源开发的信徒，我研究了他指出的每一个问题，并假定它是有益的。“好吧，这看起来很傻，但他们这么做一定有充分的理由。”当然，没有，除非你认为懒惰或无知是一个好理由。

对于一个在开源开发的公众监督下产生的项目来说，这些确实是令人不安的基本问题。对我来说，最重要的问题不是 Tomcat 是否糟糕，而是这种水平的代码质量在 Apache 阵营中是例外还是常态。

我知道一个事实，在野外有大量高质量的开源 Java 代码。例如，我已经多次研究过 [Jetty](https://www.sitepoint.com/jetty-really-is-lightweight/) 的代码，并对我在那里的发现感到惊喜。我也知道糟糕的开源代码[不仅仅是 Java](https://www.sitepoint.com/evaluating-php-applications/) 面临的问题。但是阿帕奇应该是这个领域的领导者。

如果 BileBlog 的反复谴责是一种暗示的话，那么网络上的大多数人可能都在运行这样的软件——代码看起来就像是在拼命赶时间的时候写的，而且大部分时间都是有效的……只是勉强有效。

## 分享这篇文章