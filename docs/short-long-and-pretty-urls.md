# 短，长，漂亮的网址

> 原文：<https://www.sitepoint.com/short-long-and-pretty-urls/>

URL，统一资源定位器，是网络的基础。这些是用于查找网页的地址，或者更具体地说，是“资源”，因为它们实际上不一定是网页，它们可以是图像、文件甚至原始数据中的任何内容。在大多数现代网站中，URL 已经从难以辨认的时代走了很长一段路，并且放弃了正在使用的技术，例如'/pages/show.php？' page=15&tag=ruby '。

现代的 URL 通常很短，可读性强，具有描述性，比如“/pages/tagged/with/ruby”。短 URL 的使用也有了很大的增长，比如 Bit.ly 等使用的那些。另一种经常被忽视的 url 是长 url。这些是用来避免网址被记住或容易复制。例如，如果一个网站的所有页面都是面向公众的，而你只希望人们能够访问专门发送给他们的 URL。

为了演示这三种不同的 url 方案，我将使用一个非常简单的 Sinatra & DataMapper 应用程序来创建注释。

你可以在这里看到这个应用的演示:[非常短和长](http://prettyshortandlong.heroku.com/)
源代码可以在 [GitHub](https://gist.github.com/1137353) 上找到。

## 笔记模型

每个注释都有一个标题和一些内容，可以在模型的代码中看到:

```
class Note
  include DataMapper::Resource
  property :id, Serial
  property :title, String, :required => true
  property :content, Text
end
```

## 漂亮的网址

一个“漂亮”的 url 可以被认为是人类可读的和描述性的。使用它们有一些小小的 SEO 好处，但主要原因是它们让你的 URL 看起来更专业，更容易记住。

通过向名为“pretty”的模型添加一个额外的属性，并在默认情况下根据输入的标题创建它，可以很容易地为每个笔记创建一个 pretty url。

```
property :pretty, String, default: -> r,p { r.make_pretty }
```

它使用 proc 对象在新创建的 note 对象上调用下面的 make_pretty 方法，然后将它保存到数据库中:

```
def make_pretty
  title.downcase.gsub(/W/,'-').squeeze('-').chomp('-')
end
```

这个方法获取用于标题的字符串，然后将 4 个字符串方法链接在一起，以创建漂亮的 url。下面是每个方法对示例标题“哎哟！“那真的很痛！”

**向下转换**:将所有的字母转换成小写-‘哎哟！“那真的很痛！”
**gsub(/W/，'-')** :用连字符替换所有不是字母或数字的字符-'哎哟-那真的很疼-'
**挤压('-')** :用单个连字符替换任何重复的连字符-'哎哟-那疼-'
**chomp('-')** :从末尾删除任何看起来杂乱的连字符-'哎哟-那疼'

因为这是作为 Note 类的一个属性保存的，所以可以使用`first`方法查询数据库来查找基于这个属性的注释:

```
get '/pretty/:url' do
  @note = Note.first(:pretty => params[:url])
  slim :show
end
```

## 长 URL

通过散列一些注释特有的值，可以很容易地为每个注释创建一个长 url。需要一个名为“long”的额外属性，该属性将使用 proc 来调用 make_long 方法，然后将结果字符串保存到数据库:

```
property :long, String, default: -> r,p { r.make_long }
```

它使用摘要库散列一个字符串，该字符串是通过将注释的创建时间与注释的标题和 id 连接起来而创建的。

```
def make_long
  Digest::SHA1.hexdigest(Time.now.to_s + self.title + self.id.to_s)
end
```

id 用于确保该字符串是唯一的，时间戳将使随机猜测变得困难。我选择使用 SHA1 库，它创建了一个 40 个字符的字符串，但是还有其他的如 MD5、SHA2 和 BCRYPT。

使用长 URL 的注释可以通过与漂亮 URL 相同的方式找到:

```
get '/long/:url' do
  @note = Note.first(:long => params[:url])
  slim :show
end
```

## 短 URL

为每个便笺创建一个短 url 的最简单的方法是简单地使用便笺的 id 属性作为 url(例如'/3 '将是 id 为 3 的便笺的 url)。不幸的是，这种方法至少有两个缺点:首先，随着笔记数量的增长，url 的长度也将增长——一旦超过一百万个笔记，URL 将变成 7 位或更多位。其次，如果您只是简单地使用自动递增的 id 作为 url，那么用户可能会试图更改值，希望找到另一个不适合他们的注释(假设没有密码保护)。例如，如果有人给我发了一个链接，指向一个网址为“/17”的笔记，那么我可能会想看看网址“/15”和“/16”。

第一个问题可以通过改变基数来解决。通过将 id 更改为基数为 36 的数字，您将显著减少所需的位数。基数 36 的数字使用所有的数字 0-9 和所有的字母 a-z(仅小写)来表示数字。例如，36 进制的数字 1000000 是 *lfls* 。Ruby 有一个简洁的内建方法来改变一个数的基数——你只需要把你想要转换的基数作为一个参数添加到`to_s` Integer 方法中，例如`1000000.to_s(36) => “lfls”`正如你所看到的，返回一个字符串。要变回原样，使用 string 的`to_i`方法和相同的参数，例如`”lfls”.to_i(36) => 1000000`。如您所见，这将一个 7 位数的数字缩减为 4 个字符的字符串。

我们仍然没有解决第二个问题——人们试图猜测其他网址。例如，如果 URL“/lfls”指向第一百万个笔记，那么我可以通过键入“lflt”很容易地找到下一个笔记，这是 1000001 的 36 进制表示。为了伪装这些短 URL，我们首先需要创建一个随机的 1 位数，它将作为 salt 存储在数据库中:

```
property :salt, String, default: -> r,p { (1+rand(8)).to_s }
```

你可以用下面的方法创建一个看起来非常随机的短 url:

```
def short
  id.to_s + (salt.to_s).reverse.to_i.to_s(36)
end
```

这将获取 id，将其更改为一个字符串，然后将 salt 值连接到末尾，然后将其反转，再更改为 base 36。这使得具有连续 id 的便笺具有非常不同的短 URL 外观。以上面的 1000000 和 1000001 为例:

```
(1000000.to_s + (1+rand(8)).to_s).reverse.to_i.to_s(36) => "zq0ap"
(1000001.to_s + (1+rand(8)).to_s).reverse.to_i.to_s(36) => "1c8401"
```

你确实牺牲了一点点长度，因为 salt 使得结果 url 更长，但是我觉得为了获得看起来随机的短 URL，这是值得的。您可以通过使用 base 62 数字(它们也使用所有大写字母 A-Z)使 URL 更短，但是您必须使用外部库，例如 [Base 62 gem](http://rubygems.org/gems/base62.) 。

短 url 实际上不必保存到数据库中，因为它们有一个简单的反函数，可以应用于短 URL 字符串，将其映射回注释的原始 id。

```
url.to_i(36).to_s.reverse.chop
```

这将字符串转换回一个基数为 10 的整数，然后再转换回一个字符串，对其进行反转并截掉最后一位数字(这是随机的 salt 值)。然后，可以使用 DataMapper 的 get 方法通过其 id 来查找注释:

```
get '/short/:url' do
  @note = Note.get params[:url].to_i(36).to_s.reverse.chop
  slim :show
end
```

我希望你已经发现这是有用的。留下评论，告诉你如何使用这些技术，或者其他写 URL 的方法。

## 分享这篇文章