# 谁能休息？

> 原文：<https://www.sitepoint.com/who-gets-rest/>

看着[催化剂](https://www.sitepoint.com/catalyst-impressions/)和[亨德尔](http://handelframework.com/)的提示，让我再次思考整个[休息](http://en.wikipedia.org/wiki/Representational_State_Transfer)的事情。

在我看来，很少有人明白这一点(我并不声称我明白)。有很多聪明的辩论，有些甚至脚踏实地，但是休息到底意味着什么呢？web 开发人员可以使用的实际结果是什么？我们如何消除灰色区域？[这个](http://handelframework.com/demos/catalyst/cart/list/)是休息吗？

我非常确定的是[罗伊](http://en.wikipedia.org/wiki/Roy_Fielding)和[描述的](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)本质上是一套非常简单的想法，基于[简单的协议](http://en.wikipedia.org/wiki/HTTP)，提供你需要的一切。但是在无休止的辩论中[对 SOAP](http://www.google.ch/search?q=REST+vs+SOAP) 这一点似乎已经失去了。

维基百科解释中的一个特别的引用…

> REST […]强调的是资源的多样性，即*名词*

反过来说，这是一个我们可以实际使用的大胆声明，如何？

永远不要把动词放在 URL 中

以至于做出了[这个改变](http://en.wikipedia.org/w/index.php?title=Representational_State_Transfer&diff=28989164&oldid=27110193)以至于这个网址——http://www.example.org/users/**搜索**？姓氏=迈克尔斯变成了这样:http://www.example.org/users?surname=Michaels

我将以两个例外开始(但我认为是唯一的例外)——[域名](http://www.update.com/)或动词实际上是名称的一部分(如“搜索引擎友好”URL 中的标题)如[这个](http://www.tldp.org/HOWTO/Kernel-HOWTO/)

我认为，如果围绕这个规则设计 API，那么这个规则的结果会使框架更容易使用——这是在[这个](http://www.mnot.net/blog/2003/12/08/http_api)和像 [Catalyst](http://catalyst.perl.org) 和 [Rails](http://www.rubyonrails.org/) 这样的框架之间的中间地带，我认为这些框架已经将问题转化为[这种形式](http://jobs.rubynow.com/jobs/show/70)。

在催化剂方面，归结为控制器，如:

```
 package MyApp::Controller::Cart::Item;

use strict;
use base 'Catalyst::Base';

sub new : Regex('^(d+)$') {
     my $class = shift;
     my $self = Catalyst::Base->new();
     bless($self, $class);
     return $self;
}

sub GET {
    my ( $self, $c ) = @_;
    # Do HTTP GET stuff here
}

sub POST {
    my ( $self, $c ) = @_;
    # Do HTTP POST stuff here
}

sub DELETE {
    my ( $self, $c ) = @_;
    # perhaps...
} 

```

会把它留在那里。随意张贴火焰或任何东西

## 分享这篇文章