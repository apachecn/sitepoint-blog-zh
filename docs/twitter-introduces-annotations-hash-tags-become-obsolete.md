# Twitter 引入注释；散列标签变得过时了

> 原文：<https://www.sitepoint.com/twitter-introduces-annotations-hash-tags-become-obsolete/>

在最近的 Twitter 开发者大会上宣布的新发展之一是“注释”——一种给你的推文添加额外元数据的方式。注释提供了一种简单的方法来获取那些微小的额外数据，这些数据通常会占用 140 个字符的 tweet 空间，并将它们放在一个有逻辑、有组织且支持搜索的地方，供开发人员访问。Twitter 没有明确定义注释的确切用途，或者如何使用注释来鼓励创新，但在公告中提供了几个例子。他们的主要例子是关于一本书的推文，以及注释可以提供的附加信息，比如 isbn 号和 Amazon.com 链接。

```
'annotations':
{
  'iso':
  {
    'isbn': '030759243X'
  },
  'amazon':
  {
    'url': 'http://www.amazon.com/Although-Course-You-Becoming-Yourself/dp/030759...
  }
}
```

## 这将如何工作？

附加数据位于名称空间/键/值设置中，上面的例子包含一个带有 isbn 键和值的 ISO 名称空间，并将随 tweet 一起从 Twitter API 返回。创建新的 tweet 时必须提供注释，并且不能在以后添加或更改。Twitter 没有定义开发人员应该如何使用新数据，因此将由他们在自己的应用程序中创建规则和结构，以创建和查看注释，尽管我担心这可能会导致问题(见下文)。如果您以前使用过 Twitter API，访问这个新特性并不困难，因为它将随主 tweet 有效负载一起以您所要求的任何格式提供。起初，注释将被限制在 512 字节，但 Twitter 承认他们在这方面很灵活，最终可能会达到 2Kb。考虑到 512 字节可能包含多达 512 个额外字符的信息，这是一个巨大的数字；这是实际推特限额的三倍。

最终用户体验将是无缝的，因为开发人员以透明的方式将这些新功能构建到他们的应用程序中。期待看到你的网址，散列标签和其他信息的不同字段，并在你的推文下显示给你。

## 这能用来做什么？

注释的可能性非常大，去掉常用的散列标签是我希望看到的第一个实现。如果你不熟悉标签，它们是以#topic 的形式添加到推文末尾的值，用于为人们提供一种在 Twitter 上搜索类似主题的推文的方式，这是一种对 Twitter 进行分类的非正式方式；例如，最近的推文使用了讽刺性的哈希标签#ashtag，关于巨大的火山灰云取消了欧洲各地的航班，例如“英国政府:英国领空至少再关闭 24 小时#ashtag”。

标签的问题一直是，它们会蚕食你已经有限的 140 个字符的配额，标签开始接管一条推文并不罕见！通过将你的标签作为一条推文的注释的一部分，它可以将推文释放出来用于重要的事情:内容。

### 我能想到的这些注释的其他用途

**链接**:大量的网址产生了过多的网址缩短服务，旨在创建最小字符的网址，以适应你 140 个字符的推文。现在可以使用注释来附加这些链接，并将它们保留在内容之外。还可以根据链接目的地的内容对链接进行分类。

```
'annotations':
{
  'links':
  {
    'web': 'http://www.xyz.com',
    'image' : 'http://www.abc.com',
    'video' : 'http://www.foo.com/'
  }
}
```

**扩展推文**:推文限制为 140 个字符，注释限制为 512 个字符，看到额外的数据被用作扩展推文信息的方式，我不会感到惊讶，有点像“阅读更多”对于一篇文章的意义，使推文本身只是全部内容的摘录。

```
'annotations':
{
  'extended':
  {
    'continued': 'and that is why I will never arm wrestle a goat again.'
  }
}
```

语言:想象一下能够用多种语言发布一条推文！我认为这是新注释特性更令人兴奋的用途之一，例如，你可以根据上下文信息提供一条 tweet 的多个版本；语言。想象一下，看到下面有各种旗帜的推文，只需点击一个旗帜就可以改变语言。

```
'annotations':
{
  'languages':
  {
    'en' : 'Has anyone seen my crepe maker? I last saw it at Easter.'
    'fr': 'N'importe qui a vu mon fabricant de crêpe ? Je dernier l'a vu aux Pâques. ',
    'de' : "Hat jemand meinen crepe Hersteller gesehen? Ich habe zuletzt es an Ostern gesehen. "
  }
}
```

对于极客类型的人来说，你甚至可以试着适应整个程序或功能。

```
'annotations':
{
  'code':
  {
    'javascript': 'function(){var d=0;setInterval(function() {document.body.style['-webkit-transform']= 'rotate('+ d +'deg)';d+=1},10)};
  }
}
```

我见过的其他例子包括天气、你的位置、当前听的音乐/看的电视/电影以及你的脸书/社交媒体个人资料信息。

## 我能看到出现的问题

我担心，虽然这一新功能令人难以置信地强大，但它可能会把 Twitter 和 Twitter 应用带入一个类似于西部荒野的时代。这些新注释的格式没有正式的结构或主持机构，应用程序开发人员必须非常小心，不要相互抵触。让一个应用程序实现一种向推文添加链接的方式，而让另一个应用程序以完全不同的方式这样做不会让任何人受益，因为额外的信息只会提供给正确读取这些信息的应用程序。

最终用户不应该负责知道他们的元数据的“兼容性”。如果我正在发推文，我在一些关于一本书的信息上做了注释；我不需要知道每个用户是否真的会在他们选择的 Twitter 阅读器上获得额外的信息；如果你在推文中引用或依赖元数据中的信息，情况会更糟。一条推文说“刚刚读完这本书，检查一下”并假设客户有能力阅读带有书籍信息的注释，这将导致不支持该格式的客户产生大量混乱。

我担心，通过本质上为开发人员创建一个系统来创建新功能，Twitter 将使其他开发人员的生活越来越困难，因为随着他们的应用程序被广泛采用，他们必须为他们的应用程序建立功能支持；并且使消费者的生活变得困难，因为他们必须理解一些用户可能无法访问他们想要使用的这些新功能。

## 叫我老古董，但是…

我喜欢 Twitter 的一点是它简单的口才，你只需要 140 个字符就可以传达你的信息。转发和散列标签的复杂性很有趣，因为它没有正式的结构，你只需要在这里或那里添加一些文本，瞧；人们会明白你的意思！从一条推文中挤出最后一个字符通常意味着剔除其他信息，这最终会导致一条推文尽可能直截了当、切题。Twitter 已经把我们转发的方式形式化了，但是我不用它；相反，我更愿意在推文前面加上我的“RT @username ”;我认为，随着世界转向带注释的类别，我仍将继续使用标签。

## 最后

Twitter 为开发人员创造了一种非常可扩展的方式，可以在 Twitter 中创建新功能，并以以前不可能的方式进行创新。你对新的注释特性有什么看法，你会用它来做什么？

在 Twitter 开发者 Google Group 阅读整个声明。

## 分享这篇文章