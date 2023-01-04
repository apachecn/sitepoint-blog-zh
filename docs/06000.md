# 在 Ruby 中制作链接

> 原文：<https://www.sitepoint.com/making-links/>

![Organization Solutions](img/1bdff2892713e84c524a2db46885db92.png)

你有一个应用程序，其中有一个注释字段，但你不允许在该字段中使用 HTML。用户希望能够在该字段中添加链接。实现一个链接发现特性怎么样？

听起来不错。我从哪里开始？

假设你想做一个笔记，上面写着“https://www.sitepoint.com/ruby/网址应该是一个链接”，这个网址应该变成一个链接。

## 入门指南

让我们创建一个新的 Ruby 文件

```
$ touch make_url.rb
```

打开新文件并添加以下代码。

*make_url.rb*

```
NOTE = 'The url https://www.sitepoint.com/ruby/ should be a link'  
puts NOTE
```

当您运行该代码时，您将看到注释输出到屏幕上。

```
$ ruby make_url.rb
The url https://www.sitepoint.com/ruby/ should be a link
```

那真的不会让我们有任何进展。我们需要看看这个字符串中是否有 URL。一种方法是[分割](http://ruby-doc.org/core-2.0.0/String.html#method-i-split)字符串，查看每个单词，看它们是否匹配 URL 的模式。

让我们把字符串分开，看看单个的单词。

*make_url.rb*

```
NOTE = 'The url https://www.sitepoint.com/ruby/ should be a link'  

def hasURL(note)
  words = note.split
  words.each do |word|
    puts word
  end
end

hasURL(NOTE)
```

运行它，你会看到每个单词。

```
$ ruby make_url.rb
The
url
https://www.sitepoint.com/ruby/
should
be
a
link
```

Split 获取字符串并将它拆分成一个数组。拆分的默认分隔符是空白。这给了我们一个单词数组，我们可以根据 URL 模式单独进行测试。

*make_url.rb*

```
require 'uri'

NOTE = 'The url https://www.sitepoint.com/ruby/ should be a link'

def isURL(word)
  if word =~ URI::regexp
    puts '---- we have a URL! => ' + word
  else
    puts word
  end
end

def hasURL(note)
  words = note.split
  words.each do |word|
    isURL(word)
  end
end

hasURL(NOTE)
```

让我们运行它，看看我们得到了什么。

```
$ ruby make_url.rb
The
url
---- we have a URL! => https://www.sitepoint.com/ruby/
should
be
a
link
```

有用！你可能想知道那个 [`URI::regexp`](http://www.ruby-doc.org/stdlib-2.0/libdoc/uri/rdoc/URI.html) 是做什么的。它检查是否是一个类似 URI 的字符串，如果我们有一个 URL，则返回 true。露比很棒。

我们可以用我们的新知识来创建链接。

*make_url.rb*

```
require 'uri'

NOTE = 'The url https://www.sitepoint.com/ruby/ should be a link'

def makelink(url)
  puts '<a href="' + url + '">' + url + '</a>'
end

def isURL(word)
  if word =~ URI::regexp
    makelink(word)
  else
    puts word
  end
end

def hasURL(note)
  words = note.split
  words.each do |word|
    isURL(word)
  end
end

hasURL(NOTE)
```

这导致

```
$ ruby make_url.rb 
The
url
<a href="https://www.sitepoint.com/ruby/">http:// www.sitepoint.com/ruby/</a>
should
be
a
link
```

我们正在接近。

## 重构

如果我们使用[地图](http://www.ruby-doc.org/core-2.0.0/Array.html#method-i-map)来遍历每个单词会怎么样？在 *make_url.rb* 文件中注释掉`hasURL(note)`并添加

```
# hasURL(NOTE)
puts NOTE.split.map { |x| x }
```

保存文件并运行它。

```
$ ruby make_url.rb 
The
url
https://www.sitepoint.com/ruby/
should
be
a
link
```

就输出而言，这是一种倒退，现在我们可以删除一些代码。移除`hasURL`方法。

我们可以直接内联检查，而不是将检查发送给方法。既然我们这样做了，我们也可以移除`isURL`方法并清理`makeURL`方法。

```
require 'uri'

NOTE = 'The url https://www.sitepoint.com/ruby/ should be a link'

def makelink(url)
  '<a href="' + url + '">' + url + '</a>'
end

puts NOTE.split.map { |x| x =~ URI::regexp ? makelink(x) : x }
```

让我们运行代码。

```
$ ruby make_url.rb
The
url
<a href="https://www.sitepoint.com/ruby/">https://www.sitepoint.com/ruby/</a>
should
be
a
link
```

现在，您正在拆分、遍历每个单词，并检查该单词是否是 URL。如果有，做个链接。我们有和以前一样的输出，但是我们删除了一半的代码。

## 让乐队重聚

这很酷，但是音符仍然是一个数组。让我们一起回去吧。

```
puts NOTE.split.map { |x| x =~ URI::regexp ? makelink(x) : x }.join(" ")
```

运行代码，享受简单。

```
$ ruby make_url.rb 
The url <a href="https://www.sitepoint.com/ruby/">https://www.sitepoint.com/ruby/</a> should be a link
```

太棒了。

我们有的密码。

```
require 'uri'

NOTE = 'The url https://www.sitepoint.com/ruby/ should be a link'

def makelink(url)
  puts '<a href="' + url + '">' + url + '</a>'
end

def isURL(word)
  if word =~ URI::regexp
    makelink(word)
  else
    puts word
  end
end

def hasURL(note)
  words = note.split
  words.each do |word|
    isURL(word)
  end
end

hasURL(NOTE)
```

我们掌握的密码。

*make_url.rb*
需要' uri '

```
NOTE = 'The url https://www.sitepoint.com/ruby/ should be a link'

def makelink(url)
  '<a href="' + url + '">' + url + '</a>'
end

puts NOTE.split.map { |x| x =~ URI::regexp ? makelink(x) : x }.join(" ")
```

干净多了。

当我第一次开始使用 Ruby 时，我会看到我们刚刚编写的代码，我会挠头。现在，我们开始编写这样的代码，我们明白这些简洁的语句中发生了什么。

Ruby 充满了实用程序和方法，让我们可以非常简洁地实现我们的目标。在这里，我们扫描类似 URL 的字符串，并为每个字符串创建 HTML 链接。这只用了几行 Ruby 就完成了。

您可以使用这个工具来存储带有链接数组的注释，或者简单地以 HTML 友好的方式显示注释。

## 分享这篇文章