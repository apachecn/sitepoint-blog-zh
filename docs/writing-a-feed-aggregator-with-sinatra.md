# 用 Sinatra 编写提要聚合器

> 原文：<https://www.sitepoint.com/writing-a-feed-aggregator-with-sinatra/>

谁不喜欢日落？在你看过的所有关于日落的网站中，你觉得没有一个做得好。你决定在网上做最好的日落网站。有一堆日落的照片。我们可以使用公共图像来填充这个新的令人敬畏的网站吗？在律师解决最后一个问题的时候，我们会创建一个网站。

## 孩子们还在谈论 API 吗？

有人可能会想“我打赌 Flickr 有一些很棒的日落照片。”的确如此。我们将挑选[这个组](http://www.flickr.com/groups/flickrsbestsunsets/pool/)。在页面底部，你会看到一个 RSS 提要。我们能阅读这些信息，显示这些图像，并把功劳归于适当的人吗？

是的，是的，我们可以。

让我们用我们的朋友 [Sinatra](http://www.sinatrarb.com/) 做我们的站点，用 rack/test 做测试。

安装 Sinatra 和机架/测试

```
gem install sinatra
gem install rack-test
```

我们将在其中创建一个 **feed_aggregator** 和一个 **test** 文件夹。同时，创建一个空白的主文件和测试文件。

```
$ mkdir feed_aggregator 
$ mkdir feed_aggregator/test 
$ touch feed_aggregator/test/feed_aggregator_test.rb
```

也许我们的第一个测试是确保应用程序启动。让我们在测试文件中添加

```
require '../main'  
require 'test/unit'  
require 'rack/test'  

ENV['RACK_ENV'] = 'test'

class FeedAggregatorTest < Test::Unit::TestCase
  include Rack::Test::Methods

  def app
    Sinatra::Application
  end

  def test_it_says_feed_aggregator
    get '/'
    assert last_response.ok?
    assert_equal 'Feed Aggregator', last_response.body
  end

end
```

运行测试。

```
test$ ruby feed_aggregator_test.rb 
<internal:lib/rubygems/custom_require>:29:in `require': no such file to load -- ../main (LoadError)
	from <internal:lib/rubygems/custom_require>:29:in `require'
	from feed_aggregator_test.rb:1:in `<main>'
```

耶！我们测试失败了。它说没有主文件。继续制作那个文件。

```
feed_aggregator $ touch main.rb
```

```
test$ ruby feed_aggregator_test.rb 
Loaded suite feed_aggregator_test
Started
E
Finished in 0.001054 seconds.

  1) Error:
test_it_says_feed_aggregator(FeedAggregatorTest):
NameError: uninitialized constant FeedAggregatorTest::Sinatra
    feed_aggregator_test.rb:11:in `app'
    /Users/johnivanoff/.rvm/gems/ruby-1.9.2-p180@feed_aggregator/gems/rack-test-0.6.2/lib/rack/test/methods.rb:31:in `build_rack_mock_session'
    /Users/johnivanoff/.rvm/gems/ruby-1.9.2-p180@feed_aggregator/gems/rack-test-0.6.2/lib/rack/test/methods.rb:27:in `rack_mock_session'
    /Users/johnivanoff/.rvm/gems/ruby-1.9.2-p180@feed_aggregator/gems/rack-test-0.6.2/lib/rack/test/methods.rb:42:in `build_rack_test_session'
    /Users/johnivanoff/.rvm/gems/ruby-1.9.2-p180@feed_aggregator/gems/rack-test-0.6.2/lib/rack/test/methods.rb:38:in `rack_test_session'
    /Users/johnivanoff/.rvm/gems/ruby-1.9.2-p180@feed_aggregator/gems/rack-test-0.6.2/lib/rack/test/methods.rb:46:in `current_session'
    feed_aggregator_test.rb:15:in `test_it_says_feed_aggregator'

1 tests, 0 assertions, 0 failures, 1 errors, 0 skips
```

让我们为一个只写着“提要聚合器”的应用程序添加代码

```
require 'sinatra'

get '/' do
  "Feed Aggregator"
end
```

你认为现在考试会通过吗？让我们找出答案。

```
test$ ruby feed_aggregator_test.rb 
Loaded suite feed_aggregator_test
Started
.
Finished in 0.063198 seconds.

1 tests, 2 assertions, 0 failures, 0 errors, 0 skips
```

测试通过了。

现在我们去拿那份资料。因为我们是通过测试进行开发的，所以拥有一个 RSS 提要是一个很好的主意。继续为它们创建一个目录和空白 xml 文件。

```
test$ mkdir fixtures 
test$ touch fixtures/feed.xml
```

非常好。让我们将提要的源复制到 **feed.xml** 文件中。如果我们针对实时提要进行测试，条目可能会发生变化，从而使测试变得非常令人沮丧。谁需要那个？

```
<?xml version="1.0" encoding="utf-8"?>
<rss version="2.0"
	    xmlns:media="http://search.yahoo.com/mrss/"
	    xmlns:dc="http://purl.org/dc/elements/1.1/"
	    xmlns:creativeCommons="http://cyber.law.harvard.edu/rss/creativeCommonsRssModule.html"
	    	    xmlns:flickr="urn:flickr:user" >
	<channel>

		<title>Flickr's Best Sunsets Pool</title>
		<link>http://www.flickr.com/groups/flickrsbestsunsets/pool/</link>
... Content elided ...
```

(注意:整个 XML 文件可以在 [this gist](http://gist.github.com/4180003) 中找到。
哇！我们从哪里开始？为什么我们不寻找链接，这样人们就可以点击在 Flickr 上看到原始照片。

如果您查看 XML，您会看到每张图片都在一个项目部分中。链接标签会把我们带到照片的页面。基于此，我们需要查看提要，找到项目，并找到项目中的链接。

这听起来是个很棒的测试。写下来。

```
def test_find_the_link
  feed = File.read('fixtures/feed.xml')
  items = parse feed
  item = items.first
  link = 'http://www.flickr.com/photos/mattcaustin/8205498382/in/pool-1373979@N22'
  assert_equal item[:link], link
end
```

我从哪里得到链接变量的文本？我复制了夹具中第一个项目的链接。你很快就会看到它是如何使用的。

就像我之前说的，我们将加载并解析 fixture。代码将在第一项中查找链接节点，获取它的文本，将其与我们的链接变量进行比较。应该吻合。

让我们进行测试。我希望它失败。

```
test$ ruby feed_aggregator_test.rb 
Run options: 

# Running tests:

E.

Finished tests in 0.025705s, 77.8059 tests/s, 77.8059 assertions/s.

  1) Error:
test_find_the_link(FeedAggregatorTest):
NoMethodError: undefined method `parse' for #<FeedAggregatorTest:0x007ff7412ac390>
    feed_aggregator_test.rb:24:in `test_find_the_link'

2 tests, 2 assertions, 0 failures, 1 errors, 0 skips
```

没有“parse”的方法

继续在 main.rb 文件中创建该方法。

```
require 'sinatra'

def parse  
end

get '/' do
  "Feed Aggregator"
end
```

重新运行测试。

```
test$ ruby feed_aggregator_test.rb 
Run options: 

# Running tests:

E.

Finished tests in 0.251661s, 7.9472 tests/s, 7.9472 assertions/s.

  1) Error:
test_find_the_link(FeedAggregatorTest):
ArgumentError: wrong number of arguments (1 for 0)
    /Users/john/Dropbox/feed_aggregator/main.rb:4:in `parse'
    feed_aggregator_test.rb:23:in `test_find_the_link'

2 tests, 2 assertions, 0 failures, 1 errors, 0 skips
```

我们没有传递任何论点。这很好，因为我们只想解决最后一个错误。你会怎么做才能过关？回到 main.rb 文件

```
require 'sinatra'

def parse feed
end

get '/' do
  "Feed Aggregator"
end
```

你认为那样会摆脱*论据*的错误吗？重新运行测试，看看。

```
test$ ruby feed_aggregator_test.rb 
Run options: 

# Running tests:

E.

Finished tests in 0.027235s, 73.4349 tests/s, 73.4349 assertions/s.

  1) Error:
test_find_the_link(FeedAggregatorTest):
NoMethodError: undefined method `first' for nil:NilClass
    feed_aggregator_test.rb:24:in `test_find_the_link'

2 tests, 2 assertions, 0 failures, 1 errors, 0 skips
```

的确如此。现在我们需要一个“第一”方法。

为了获得第一个链接，我们需要搜索整个 XML。

现在，有什么方法可以解析 XML 来找到这个链接呢？我喜欢(Nokogiri)[http://nokogiri.org]。你安装了那个宝石吗？让我们检查一下。你的输出可能和我的不一样。

```
test$  gem list --local -d noko

*** LOCAL GEMS ***
```

显然我不知道。如果没有，就继续安装吧。

```
test$ sudo gem install nokogiri
```

除了在终端上看到它成功安装，你如何检查它是否安装了？

```
test$  gem list --local -d noko

*** LOCAL GEMS ***

nokogiri (1.5.5)
    Authors: Aaron Patterson, Mike Dalessio, Yoko Harada, Tim Elliott
    Rubyforge: http://rubyforge.org/projects/nokogiri
    Homepage: http://nokogiri.org
    Installed at: /Users/john/.rvm/gems/ruby-1.9.3-p194@feed_aggregator

    Nokogiri (é‹¸) is an HTML, XML, SAX, and Reader parser
```

我们在哪里？解析 XML 文档。你需要把信号输入到 Nokogiri。然后你可以遍历每一项，得到链接，并把它们存储在一个 hash 中。不要忘记归还物品。

```
require 'sinatra'
require 'nokogiri'

def parse feed
  doc = Nokogiri::XML feed
  doc.search('item').map do |doc_item|
    item = {}
    item[:link] = doc_item.at('link').text
    item
  end
end

get '/' do
  "Feed Aggregator"
end
```

你对此有好感吗？继续运行测试。你记得在 main.rb 中包含 nokogiri 吗？

```
test$ ruby feed_aggregator_test.rb
Loaded suite feed_aggregator_test
Started
..
Finished in 0.112374 seconds.

2 tests, 3 assertions, 0 failures, 0 errors, 0 skips
```

[*史努比舞*](http://www.mikesfreegifs.com/main4/funny/schroeder2.gif) 我们能这么说吗？

我们接下来需要得到缩略图。在夹具的哪个位置？你找到了吗？它是标签中的一个属性。继续为此编写一个测试。和第一个很接近。

```
def test_find_the_thumbnail_image
  feed = File.read('fixtures/feed.xml')
  items = parse feed
  item = items.first
  thumbnail = 'http://farm9.staticflickr.com/8488/8205498382_4e5ed09a62_s.jpg'
  assert_equal item[:thumbnail], thumbnail
end
```

运行测试。

```
test$ ruby feed_aggregator_test.rb 
Run options: 

# Running tests:

.F..

Finished tests in 0.053503s, 74.7622 tests/s, 93.4527 assertions/s.

  1) Failure:
test_find_the_thumbnail_image(FeedAggregatorTest) [feed_aggregator_test.rb:42]:
<nil> expected but was
<"http://farm9.staticflickr.com/8488/8205498382_4e5ed09a62_s.jpg">.

4 tests, 5 assertions, 1 failures, 0 errors, 0 skips
```

这是意料之中的。让我们好好想想。我们需要`media:thumbnail`节点的属性值。这个怎么样？

```
require 'sinatra'
require 'nokogiri'

def parse feed
  doc = Nokogiri::XML feed
  doc.search('item').map do |doc_item|
    item = {}
    item[:link] = doc_item.at('link').text
    item[:thumbnail] = doc_item.at('media:thumbnail').attr('url').value
    item
  end
end

get '/' do
  "Feed Aggregator"
end
```

这是有意义的，因为我们需要的 url 是该节点的一个属性。去试试吧。

```
test john$ ruby feed_aggregator_test.rb 
Run options: 

# Running tests:

EE.

Finished tests in 0.031776s, 94.4109 tests/s, 62.9406 assertions/s.

  1) Error:
test_find_the_link(FeedAggregatorTest):
NoMethodError: undefined method `attr' for nil:NilClass
    /Users/john/Dropbox/feed_aggregator/main.rb:10:in `block in parse'
    /Users/john/.rvm/gems/ruby-1.9.3-p194@feed_aggregator/gems/nokogiri-1.5.5/lib/nokogiri/xml/node_set.rb:239:in `block in each'
    /Users/john/.rvm/gems/ruby-1.9.3-p194@feed_aggregator/gems/nokogiri-1.5.5/lib/nokogiri/xml/node_set.rb:238:in `upto'
    /Users/john/.rvm/gems/ruby-1.9.3-p194@feed_aggregator/gems/nokogiri-1.5.5/lib/nokogiri/xml/node_set.rb:238:in `each'
    /Users/john/Dropbox/feed_aggregator/main.rb:7:in `map'
    /Users/john/Dropbox/feed_aggregator/main.rb:7:in `parse'
    feed_aggregator_test.rb:23:in `test_find_the_link'

  2) Error:
test_find_the_thumbnail_image(FeedAggregatorTest):
NoMethodError: undefined method `attr' for nil:NilClass
    /Users/john/Dropbox/feed_aggregator/main.rb:10:in `block in parse'
    /Users/john/.rvm/gems/ruby-1.9.3-p194@feed_aggregator/gems/nokogiri-1.5.5/lib/nokogiri/xml/node_set.rb:239:in `block in each'
    /Users/john/.rvm/gems/ruby-1.9.3-p194@feed_aggregator/gems/nokogiri-1.5.5/lib/nokogiri/xml/node_set.rb:238:in `upto'
    /Users/john/.rvm/gems/ruby-1.9.3-p194@feed_aggregator/gems/nokogiri-1.5.5/lib/nokogiri/xml/node_set.rb:238:in `each'
    /Users/john/Dropbox/feed_aggregator/main.rb:7:in `map'
    /Users/john/Dropbox/feed_aggregator/main.rb:7:in `parse'
    feed_aggregator_test.rb:31:in `test_find_the_thumbnail_image'

3 tests, 2 assertions, 0 failures, 2 errors, 0 skips
```

嗯，那次失败了。*nil:nil class*的未定义方法‘attr’没有找到`<media:thumbnail>`节点。如果您查看 RSS 提要的顶部，可以看到使用了媒体名称空间。 [*更多关于名称空间。*](http://en.wikipedia.org/wiki/XML_namespaces)

事实证明，使用 Nokogiri，您可以只使用管道符号来表示名称空间搜索。继续交换冒号，并用缩略图行中的管道替换它。

```
require 'sinatra'
require 'nokogiri'

def parse feed
  doc = Nokogiri::XML feed
  doc.search('item').map do |doc_item|
    item = {}
    item[:link] = doc_item.at('link').text
    item[:thumbnail] = doc_item.at('media|thumbnail').attr('url')
    item
  end
end

get '/' do
  "Feed Aggregator"
end
```

试试看。重新运行测试

```
test$ ruby feed_aggregator_test.rb 
Run options: 

# Running tests:

...

Finished tests in 0.045266s, 66.2749 tests/s, 88.3665 assertions/s.

3 tests, 4 assertions, 0 failures, 0 errors, 0 skips
```

太棒了。我们可能也应该使用图片的标题。继续为此编写测试。我会等的。

完了？我是这么做的。

```
def test_find_the_title
  feed = File.read('fixtures/feed.xml')
  items = parse feed
  item = items.first
  title = 'An Evening at Shell Beach'
  assert_equal item[:title], title
end
```

同样，我们使用的标题来自我们装置的第一个项目。运行测试。

```
test$ ruby feed_aggregator_test.rb 
Run options: 

# Running tests:

.F.

Finished tests in 0.078211s, 38.3578 tests/s, 51.1437 assertions/s.

  1) Failure:
test_find_the_title(FeedAggregatorTest) [feed_aggregator_test.rb:34]:
<nil> expected but was
<"An Evening at Shell Beach">.

3 tests, 4 assertions, 1 failures, 0 errors, 0 skips
```

我们需要寻找标题。如何将它添加到解析方法中？

```
require 'sinatra'
require 'nokogiri'

def parse feed
  doc = Nokogiri::XML feed
  doc.search('item').map do |doc_item|
    item = {}
    item[:link] = doc_item.at('link').text
    item[:thumbnail] = doc_item.at('media|thumbnail').attr('url')
    item[:title] = doc_item.at('title').text
    item
  end
end

get '/' do
  "Feed Aggregator"
end
```

运行测试。

```
test$ ruby feed_aggregator_test.rb 
Run options: 

# Running tests:

....

Finished tests in 0.062725s, 63.7704 tests/s, 79.7130 assertions/s.

4 tests, 5 assertions, 0 failures, 0 errors, 0 skips
```

太棒了，但是让我们看看网页上的东西。我们希望在浏览器中显示结果。

为了简单起见，我将使用 erb 制作网页。

```
require 'sinatra'
require 'nokogiri'

def parse feed
  doc = Nokogiri::XML feed
  doc.search('item').map do |doc_item|
    item = {}
    item[:link] = doc_item.at('link').text
    item[:thumbnail] = doc_item.at('media|thumbnail').attr('url')
    item[:title] = doc_item.at('title').text
    item
  end
end

get '/' do
  erb :index
end

__END__

@@index
<!DOCTYPE html>
<html>
  <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="user-scalable=yes, width=device-width" />
<title>Lovely Sunsets</title> 
</head>
<body>
  <h1>Feed Aggregator</h1>
</body>
</html>
```

因为我们做了一些改变，我们应该重新运行测试。

```
test$ ruby feed_aggregator_test.rb 
Run options: 

# Running tests:

...F

Finished tests in 0.251257s, 15.9200 tests/s, 19.8999 assertions/s.

  1) Failure:
test_it_says_feed_aggregator(FeedAggregatorTest) [feed_aggregator_test.rb:18]:
<"Feed Aggregator"> expected but was
<"<!DOCTYPE html>n<html>n  <head>n  <meta charset="UTF-8">n  <meta name="viewport" content="user-scalable=yes, width=device-width" />n<title>Lovely Sunsets</title> n</head>n<body>n  <h1>Feed Aggregator</h1>n</body>n</html>n">.

4 tests, 5 assertions, 1 failures, 0 errors, 0 skips
```

哎呀。去把它修好。

```
def test_it_says_feed_aggregator
  get '/'
  assert last_response.ok?
  assert_match 'Feed Aggregator', last_response.body
end
```

我们正在确保“提要聚合器”在页面内。

现在测试通过了，让我们继续。你重新做了测试，对吗？我们将添加提要 url，然后解析出我们的信息。

```
require 'sinatra'
require 'nokogiri'

feed = File.read('test/fixtures/feed.xml')

def parse feed
  doc = Nokogiri::XML feed
  doc.search('item').map do |doc_item|
    item = {}
    item[:link] = doc_item.at('link').text
    item[:thumbnail] = doc_item.at('media|thumbnail').attr('url')
    item[:title] = doc_item.at('title').text
    item
  end
end

get '/' do
  @pictures = parse feed
  erb :index
end

__END__

@@index
<!DOCTYPE html>
<html>
  <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="user-scalable=yes, width=device-width" />
<title>Lovely Sunsets</title> 
</head>
<body>
  <h1>Feed Aggregator</h1>
  <dl>
    <% @pictures.each do |picture| %>
      <dt><a href="<%= picture[:link] %>"><%= picture[:title] %></a></dt>
      <dd><img src="<%= picture[:thumbnail] %>" /></dd>
    <% end %>
  </dl>
</body>
</html>
```

您可能注意到我指的是测试设备，而不是 RSS 提要。同样，我不想在开发时不断地向他们的服务器请求。

继续启动服务器，在你的浏览器中查看可爱的作品。

一切看起来都很好。还是用真实数据吧。你将如何连接它来从 Flickr 获取 feed？是的，让我们将 open-uri 添加到 main.rb 文件中，然后让 nokogiri 打开该文件。

```
require 'sinatra'
require 'nokogiri'
require 'open-uri'

feed = 'http://api.flickr.com/services/feeds/groups_pool.gne?id=1373979@N22&lang=en-us&format=rss_200'

def parse feed
  doc = Nokogiri::XML(open(feed))
  doc.search('item').map do |doc_item|
    item = {}
    item[:link] = doc_item.at('link').text
    item[:thumbnail] = doc_item.at('media|thumbnail').attr('url')
    item[:title] = doc_item.at('title').text
    item
  end
end

get '/' do
  @pictures = parse feed
  erb :index
end

__END__

@@index
<!DOCTYPE html>
<html>
  <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="user-scalable=yes, width=device-width" />
<title>Lovely Sunsets</title> 
</head>
<body>
  <h1>Lovely Sunsets</h1>
  <dl>
    <% @pictures.each do |picture| %>
      <dt><a href="<%= picture[:link] %>"><%= picture[:title] %></a></dt>
      <dd><img src="<%= picture[:thumbnail] %>" /></dd>
    <% end %>
  </dl>
</body>
</html>
```

启动它并在浏览器中查看 [http://127.0.0.1:4567/](http://127.0.0.1:4567/)

太好了。现在，您可以添加错误处理、一些缓存或多个提要。

如果你想看其中一篇文章，请告诉我们。

干杯。

## 分享这篇文章