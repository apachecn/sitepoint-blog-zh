# safe html–清理表单输入

> 原文：<https://www.sitepoint.com/safehtml-cleaning-form-input/>

目前正在阅读一些与网络相关的安全书籍。

一个是由[伊万·里斯蒂奇](http://www.modsecurity.org/blog/) ( [mod_security](http://www.modsecurity.org/) )设计的[阿帕奇安全](http://www.apachesecurity.net/)，我上周末又见到了他。我将把这篇长评论留到下次再看(我还没有读完)，可以说，如果你正在做任何关于 Apache 的事情，这是一篇*必读的*。特别是 PHP 开发人员，他们倾向于只看到栈中的一小部分(“Apache 是主机的问题吗？”).

另一个是德国出版物 [PHP-Sicherheit](http://www.php-sicherheit.de/) ，作者之一是 [Christopher Kunz](http://www.christopher-kunz.de/) ，他在[会议](https://www.sitepoint.com/php-conference-uk-10th-feb-2006/)上谈论[硬化的 PHP](http://www.hardened-php.net/) 。

**加强 HTML**

我也不能说太多关于 PHP-Sicherheit 的事情，除了我喜欢我目前所看到的。让我打字的是它提到了[safe HTML](http://pixel-apes.com/safehtml/)——[罗曼·伊万诺夫](http://pixel-apes.com/)的“用 PHP 编写的反 XSS HTML 解析器”，我以前没见过。奇怪的是，它有点像 Sitepointforums 的产品，因为它使用了 [XML_HTMLSax](http://pear.php.net/package/XML_HTMLSax3) ，这基本上是在[这个线程](http://www.sitepointforums.com/showthread.php?threadid=121246)中开发的。

现在，SafeHTML 就像一个过滤器，试图过滤掉任何危险的东西。网络上的普遍观点是，实际上不可能做到这一点——例如，有太多的方法可以将“javascript”这个词作为链接的协议，而 IE(可悲的是)非常宽容。我只浏览了一下代码，但到目前为止，它看起来很有说服力。例如，这是他如何处理分析链接协议(只是类的相关位)…

```
 var $blackProtocols = array(
        'about',   'chrome',     'data',       'disk',     'hcp',     
        'help',    'javascript', 'livescript', 'lynxcgi',  'lynxexec', 
        'ms-help', 'ms-its',     'mhtml',      'mocha',    'opera',   
        'res',     'resource',   'shell',      'vbscript', 'view-source', 
        'vnd.ms.radio',          'wysiwyg', 
        );

        // ...

      foreach ($this->blackProtocols as $proto) {
          $preg = "/[sx01-x1F]*";
          for ($i=0; $i<strlen ($proto); $i++) {
              $preg .= $proto{$i} . "[sx01-x1F]*";
          }
          $preg .= ":/i";
          $this->_protoRegexps[] = $preg;
      } 

```

不仅应该匹配“javascript”，还应该匹配“java script”和许多其他可能的包含 ASCII 控制字符的组合。

它看起来也像是对 UTF-7 很聪明的 T2——还没有仔细研究过。另一个好迹象(看起来很奇怪)是“[错误报告](http://secunia.com/advisories/14403/)”，也已经被修复。

尽管如此，我还是不能完全信服——让我困惑的一件事是，它决定了 HTML get 为你剥离了什么。例如，它能处理一个宽度很大的表格标签，有效地破坏一个设计吗(好吧，那不是 XSS，而是…)？仍在调查中…如果能在类似于 [Berlios](http://www.berlios.de/) 或 [Sourceforge](http://sourceforge.net) 的地方举办，那也是件好事。

否则——旁注(也许对 Roman 来说)—[Jeff](http://www.procata.com/blog)已经用一个新的设计改进了性能(超过 HTMLSax ),发现[在这里](http://cvs.sourceforge.net/viewcvs.py/wact/wact/framework/template/compiler/htmlparser.inc.php?rev=1.16&view=auto)。

## 分享这篇文章