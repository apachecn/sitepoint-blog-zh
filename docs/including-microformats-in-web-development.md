# SEO 策略:Web 开发中的微格式

> 原文：<https://www.sitepoint.com/including-microformats-in-web-development/>

2009 年 5 月，谷歌的员工开始在他们的 SERPS 中加入丰富的信息片段。这相当于 17 个月的开发时间，说实话，似乎大多数网站还没有跟上潮流。这是一个遗憾，因为使用丰富的片段可以大大提高人们在进行搜索时点击你的网站列表的机会。

目前，丰富的片段非常简单。例如，如果你搜索一部电影，你可能会看到某些搜索结果下面有评论信息，就在结果页面上。这是可能的，因为这些网站的所有者已经在他们的页面上用一种特定的预定义格式标记了评论信息，这种格式被称为**微格式**。实际上有各种各样的元素可以用微格式进行标记。可以用更具语义的方式标记的其他项目可以更好地改进搜索引擎读取和索引您的站点的方式，这些项目是:

*   hCard:这是用来标记人、地点、企业和组织的。
*   hCalendar:这用于标记事件。
*   hReview:这一点不言自明——它用于标记评论，然后在 SERP 中显示为星级。

这些微格式都不太复杂；对于应用于标记中某些元素的类以及这些元素的嵌套方式，它们仅仅依赖于一些约定。然而，让我们来看一下每一个的示例实现，看看代码是什么样子的。

为了创建这个样本标记，我使用了一组微格式创建器来简化这个过程，这些微格式创建器可以在[microformats.org](http://microformats.org/)上找到。

## jcard

好的，我们知道 hCard 格式用于人、地点和组织。通常与 hCard 格式相关的字段有:

*   教名
*   中名
*   姓氏
*   组织
*   街道
*   城市
*   状态
*   邮编
*   国家
*   电话
*   电子邮件
*   统一资源定位器
*   图片 URL
*   AIM 屏幕名称
*   YIM 昵称
*   Jabber 屏幕名称
*   标签

包含这些字段的标记片段示例如下:

```
<div id="hcard-Kristen-Lee-Holden" class="vcard">
  <img style="float:left; margin-right:4px"
       src="author.jpg"
       alt="photo of Kristen"
       class="photo"/>
  <span class="fn n">
    <span class="given-name">Kristen</span>
    <span class="additional-name">Lee</span>
    <span class="family-name">Holden</span>
  </span>
  <div class="org">Sitepoint Pty. Ltd.</div>
  <a class="email" href="mailto:kristen.holden@sitepoint.com">kristen.holden@sitepoint.com</a>
  <div class="adr">
    <div class="street-address">48 Cambridge Street</div>
    <span class="locality">Melbourne</span>,
    <span class="region">Victoria</span>,
    <span class="postal-code">3056</span>
    <span class="country-name">Australia</span>
  </div>
  <div class="tel">90908200</div>
  <div class="tags">
    <a href="http://kitchen.technorati.com/contacts/tag/seo guru">seo guru</a>
    <a href="http://kitchen.technorati.com/contacts/tag/marketing dude">marketing dude</a>
  </div>
</div>
```

相当简单的结构和实现，不是吗？请记住，搜索引擎蜘蛛依靠这些类名来破译内容的含义，所以它们需要准确。但是一旦您修改了 CMS 以输出正确格式化的标记，您的工作就完成了。这就是为什么很难理解为什么这么多网站仍然没有使用这种微格式来正确标记信息。

## 回顾

接下来是 hReview 格式。从一个 SEO 专家的角度来看，这是我认为目前最有用的微格式。在搜索结果中显示一组正面的评论和星级，可以极大地提高访问者的信任度和点击率。

hReview 格式中使用的字段有:

*   名字
*   统一资源定位器
*   图片 URL
*   类型(产品、企业、事件、地点、人员、网站、URL)
*   评级
*   摘要
*   描述
*   审查日期
*   评论者

向您展示这在作为代码实现时是什么样子的最简单的方法是向您展示一个示例。因此，这里有一个我根据即将发布的 SitePoint SEO 商业指南的虚构评论整理出来的:

```
<div class="hreview" id="hreview-The-definitive-business-guide-for-seo-professionals-">
  <h2 class="summary">The definitive business guide for SEO professionals</h2>
  <abbr class="dtreviewed" title="2008-10-05T08:01+10:00">Oct 5, 2008</abbr>
  by <span class="reviewer vcard"><span class="fn">Joe Blogs</span></span>

  <span class="type" style="display: none; ">product</span>
  <img alt="photo of 'SEO Business Guide'" src="https://www.sitepoint.com/seo1.jpg" class="photo">
  <div class="item">
    <a class="fn url" href="https://www.sitepoint.com/seo1">SEO Business Guide</a>
  </div>
  <blockquote class="description">
  <p><abbr title="5" class="rating">★★★★★</abbr>
This book was amazing. It helped me to learn the basics of SEO and then how to apply them in a practical and easy to read ways.</p>
  </blockquote>
  <span class="version"style="display: none; ">0.3</span>
</div>
```

## vcalendar

最后，让我们快速浏览一下 hCalendar 格式中包含的字段示例。hCalendar 很像前两种格式；它的字段是:

*   摘要
*   位置
*   统一资源定位器
*   开始日期
*   结束日期
*   时区(GMT)
*   描述
*   标签

下面是 hCalendar 实现在新的 SEO 业务指南的假设发布活动中使用时的样子:

```
<div class="vevent" id="hcalendar-SEO1-Book-Launch">
  <a class="url" href="https://www.sitepoint.com/seo1">
  <abbr class="dtstart" title="2010-11-05T09:00+10:0000">November 5th 9am</abbr>,
  <abbr class="dtend" title="2010-11-06T09:00+10:00">9am 2010</abbr> :
  <span class="summary">SEO1 Book Launch</span>
  at <span class="location">Collingwood, Melbourne</span>
  </a>
  <div class="description">
  The official launch of the SEO business guide from sitepoint!
  </div>
  <div class="tags">Tags: 
    <a rel="tag" href="http://eventful.com/events/tags/seo%20book">seo book</a>
    <a rel="tag" href="http://eventful.com/events/tags/seo%20business%20guide">seo business guide</a>      
    <a rel="tag" href="http://eventful.com/events/tags/awesome%20seo%20book">awesome seo book</a>
  </div>
</div>
```

很明显，这些信息最好是从你网站的数据库中动态获取。

要了解我在这篇文章中提到的微格式的更多信息，最好的方法是访问 microfomats.org:

[http://microformats.org/wiki/hreview3](http://microformats.org/wiki/hreview3)
http://microformats.org/wiki/hcardT5[http://microformats.org/wiki/hcalendar](http://microformats.org/wiki/hcalendar)

你也可以阅读谷歌的发布文章[这里](http://googlewebmastercentral.blogspot.com/2009/05/introducing-rich-snippets.html%20)来看看这些新的丰富片段的官方说法。

所以总的来说，对于所有的开发者来说，为你的网站内容实现这些类型的格式是很容易的。除了 HTML 输出所需的日常格式之外，几乎没有额外的工作。今天构建使用微格式的网站将会给你未来本地 SEO 的各个方面带来明显的优势。

地图、日历和其他更加综合的搜索结果已经在使用这些格式。事实上，就在昨天，我读了 SEO by the Sea 的一篇很棒的博客文章,内容是关于谷歌自动将谷歌地图插入网站的最新专利申请。这将使移动用户的生活更加轻松。随着脸书、Yelp、Digg 和 Twitter 等公司支持各种微格式，随着时间的推移，很有可能会看到它们的有用性进一步增加。

也许更重要的是，这是一种无痛的方式，以相当少的开发时间投入，给组织中的 SEO 人员一些爱！

## 分享这篇文章