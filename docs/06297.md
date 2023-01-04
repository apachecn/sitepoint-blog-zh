# 辛纳特拉的电影，第一部分

> 原文：<https://www.sitepoint.com/moving-pictures-sinatra-part/>

![mp_sinatra](img/1966aa53315c826750c2e53b4d6b973a.png)

## 使用文件和目录。

今天，我想做一个网站，显示目录中的图片。我认为辛纳特拉会很适合这个。我们会安排好的。

## 图片浏览器

### 设置环境。

我喜欢 [RVM](https://rvm.io/) 因为它把宝石留在自己的半球，不会和其他项目造成永久的波澜。设置一个名为 *movingPictures* 的 gemset，使用 ruby 2.0，然后确保我们使用的是那个 gemset。您可以在一行简单的代码中完成所有这些工作。

```
$ rvm gemset use 2.0.0@movingPictures --create 
```

既然操场已经准备好了，我们为什么不用 [TDD](http://en.wikipedia.org/wiki/Test-driven_development) 来做这个呢。为应用程序创建一个目录，一个*测试*目录，和一些空白文件让我们开始。

```
$ mkdir movingPictures  
$ mkdir movingPictures/test  

$ cd movingPictures

$ touch Gemfile
$ touch main.rb
$ touch test/test.rb 
```

打开宝石文件，添加我们需要的宝石。

#### Gemfile

```
source 'http://rubygems.org'  

gem 'sinatra'  
gem 'rack-test' 
```

安装宝石

```
$ gem install bundler
$ bundle install 
```

我们现在准备出发了。编写一个测试来确保应用程序启动。

*test/test.rb*

```
require './main'
require 'minitest/autorun'
require 'rack/test'
ENV['RACK_ENV'] = 'test'

class MyTest < MiniTest::Unit::TestCase

  include Rack::Test::Methods

  def app
    Sinatra::Application
  end

  def test_for_echo
    get '/'
    assert last_response.ok?
    assert_equal "Echo", last_response.body
  end
end 
```

我们包含了运行我们的应用程序的 *main.rb* 文件，以及用于测试的文件。

#### main.rb

```
require 'sinatra'

get '/' do
  "Echo"
end 
```

安润，去做测试吧。

```
$ ruby test/test.rb 
```

太酷了，有用。

显然，应用程序需要显示一些图片。你会怎么写一个测试呢？

*test/test.rb*

```
â€¦
def test_for_pictures
    pictures = load_pictures
    assert pictures.length > 0
end
â€¦ 
```

看到我们在这里做的了吗？加载图片并确保图片数量大于零。让我们试试那个。运行测试

```
$ ruby test/test.rb
Run options: --seed 30840

# Running tests:

.E

Finished tests in 0.041759s, 47.8939 tests/s, 47.8939 assertions/s.

  1) Error:
test_for_pictures(MyTest):
NameError: undefined local variable or method `load_pictures' for #<MyTest:0x97e398>
    test/test.rb:21:in `test_for_pictures'
â€¦ 
2 tests, 2 assertions, 0 failures, 1 errors, 0 skips 
```

那很好。你需要做什么来消除这个错误？将缺少的方法`load_pictures`添加到 main.rb 文件中。一旦你这样做了，运行测试，看看你会得到什么。

### main.rb

```
require 'sinatra'

def load_pictures

end

get '/' do
  "Echo"
end 
```

让我们再做一次测试。

```
$ ruby test/test.rb
Run options: --seed 61820

# Running tests:

E.

Finished tests in 0.041278s, 48.4520 tests/s, 48.4520 assertions/s.

  1) Error:
test_for_pictures(MyTest):
NoMethodError: undefined method `length' for nil:NilClass
    test/test.rb:22:in `test_for_pictures'
â€¦ 
2 tests, 2 assertions, 0 failures, 1 errors, 0 skips 
```

太棒了。一个错误解决了，另一个又出现了。没什么大不了的，你已经显示出你擅长解决错误。让我们解决这个问题。
我们的`load_pictures`方法是否返回了任何东西？问题就在这里。

照片在哪里？我想我们没想过这个。有时候，我们不应该直接进入编码，或者我们应该吗？因为我们使用 TDD，如果我们开始移动东西，我们会发现东西是否坏了。太好了。

你需要在某个地方存储图片。在应用程序根目录下的一个目录中怎么样？让我们用*幻灯片*图片 _ 作为目录名。是的，这非常长，但是目录中包含了什么是显而易见的。

继续做那个目录。

等等！也许这就是我们需要思考的地方。我们不想仓促行事。

如果我们把这个目录从根目录中删除，那么我们将不得不做一些路由工作。如果我们在根目录下做一个[公共目录](http://www.sinatrarb.com/configuration.html#__static_files_directory)，并在里面做一个*幻灯片*图片 _ 目录，这将解决一些不必要的编码。同意的请举手。

```
$ mkdir public/
$ mkdir public/slideshow_pictures 
```

因为我们在一个目录中寻找图片，所以你需要编写代码来完成这个任务。如果你不熟悉 Ruby 中的 [Dir 类](http://ruby-doc.org/core-2.0/Dir.html),那就去看看吧。

让我们使用 [Dir#glob](http://ruby-doc.org/core-2.0/Dir.html#method-c-glob) 。那个看起来很强大。

#### main.rb

```
â€¦
def load_pictures
  Dir.glob("public/slideshow_pictures/*")
end
â€¦ 
```

进行测试。

```
$ ruby test/test.rb 
Run options: --seed 25112

# Running tests:

F.

Finished tests in 0.016690s, 119.8322 tests/s, 179.7484 assertions/s.

  1) Failure:
test_for_pictures(MyTest) [test/test.rb:22]:
Failed assertion, no message given.

2 tests, 3 assertions, 1 failures, 0 errors, 0 skips 
```

啊？*断言失败，未给出消息。*在我的代码中，第 22 行是`assert pictures.length > 0`

[assert](http://bfts.rubyforge.org/minitest/MiniTest/Assertions.html#method-i-assert) 方法允许您添加一条消息，如果 assert 为 false，就会显示这条消息。让我们滚动骨骼并给它添加一条消息，看看会发生什么。

```
assert pictures.length > 0, "There are no pictures" 
```

再次运行测试。

```
$ ruby test/test.rb 
Run options: --seed 735

# Running tests:

F.

Finished tests in 0.016786s, 119.1469 tests/s, 178.7204 assertions/s.

  1) Failure:
test_for_pictures(MyTest) [test/test.rb:22]:
There are no pictures

2 tests, 3 assertions, 1 failures, 0 errors, 0 skips 
```

那更好。让我们回到幻灯片上来。离开舞台左侧。

*幻灯片*图片 _ 目录里什么都没有。继续向该目录添加一个文件，然后重新运行测试。

```
$ touch public/slideshow_pictures/temp.txt
$ ruby test/test.rb 
Run options: --seed 1658

# Running tests:

..

Finished tests in 0.023275s, 85.9291 tests/s, 128.8937 assertions/s.

2 tests, 3 assertions, 0 failures, 0 errors, 0 skips 
```

耶！你现在证明了在*幻灯片*图片 _ 目录中有东西。它只是一个文本文件，但是我们想要 jpg。

你知道吗 *Dir#glob* 允许你使用正则表达式？

继续将`load_pictures`方法改为只查找 jpg，然后重新运行测试。

```
def load_pictures
  Dir.glob("public/slideshow_pictures/*.{jpg,JPG}")
end 
```

你得到了“没有图片”的错误吗？将 JPG 添加到*幻灯片放映*图片 _ 目录，并重新运行测试。记住，默认情况下，正则表达式是区分大小写的。

所有测试都通过了吗？太好了，现在怎么办？我想在网页上展示它。你知道该怎么做。写一个测试。

在*幻灯片*图片 _ 目录中，我有一张名为*test.jpg*的图片。当我运行测试时，图像源应该是这样的。

#### test/test.rb

```
â€¦
def test_for_echo
    get '/'
    assert last_response.ok?
    assert_equal "slideshow_pictures/test.jpg", last_response.body
end
â€¦ 
```

注意到`assert_equal`线了吗？为什么不*公开/幻灯片*图片*？一个文件*。/public/slide show*pictures/test . jpg *可作为 http://example.com/slideshow_pictures/test.jpg.[这里有一个更技术性的解释。继续运行测试。](http://www.sinatrarb.com/intro.html#Static%20Files)

```
$ ruby test/test.rb 
Run options: --seed 3602

# Running tests:

F.

Finished tests in 0.020418s, 97.9528 tests/s, 146.9292 assertions/s.

  1) Failure:
test_moving_world(MyTest) [test/test.rb:17]:
Expected: "slideshow_pictures/test.jpg"
  Actual: "Echo"

2 tests, 3 assertions, 1 failures, 0 errors, 0 skips 
```

当然失败了。你需要加载图片，然后写出它们的“路径”

*main.rb*

```
â€¦
get '/' do
  @pictures = load_pictures
  @pictures.each do |picture|
    picture
  end
end
â€¦ 
```

您将图片加载到`@pictures`实例变量中，然后遍历该变量。让我们重新进行测试。

```
$ ruby test/test.rb
Run options: --seed 52201

# Running tests:

F.

Finished tests in 0.062974s, 31.7591 tests/s, 47.6387 assertions/s.

  1) Failure:
test_moving_world(MyTest) [test/test.rb:17]:
--- expected
+++ actual
@@ -1 +1 @@
-"slideshow_pictures/test.jpg"
+"public/slideshow_pictures/test.jpg"

2 tests, 3 assertions, 1 failures, 0 errors, 0 skips 
```

呃。这就像你需要将 *【公共】/* 替换为“来自图片路径”。
有什么办法吗？

#### main.rb

```
â€¦
@pictures.each do |picture|
  picture.sub!(/public\//, '')
end
â€¦ 
```

你不再需要那个 *public/* 了，所以我们去掉了它。这应该永远不会回来咬我们，对不对？:-)
再次运行测试。

```
$ ruby test/test.rb
Run options: --seed 21438

# Running tests:

..

Finished tests in 0.043231s, 46.2631 tests/s, 69.3946 assertions/s.

2 tests, 3 assertions, 0 failures, 0 errors, 0 skips 
```

转眼间。起作用了。现在，您可以为浏览器编写代码来显示图片。有了 Sinatra，我们可以使用[内联模板](http://www.sinatrarb.com/intro.html#Inline%20Templates)来呈现我们的输出。内联模板是在主应用程序文件中定义的。它们位于文件的底部。

#### main.rb

```
require 'sinatra'

def load_pictures
  Dir.glob("public/slideshow_pictures/*.{jpg,JPG}")
end

get '/' do
  @pictures = load_pictures
  erb :index
end

__END__

@@index
<!DOCTYPE html>
<html>
  <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="user-scalable=yes, width=device-width" />
<title>Moving Pictures</title>
</head>
<body>
  <% @pictures.each do |picture| %>
    <img src="<%= picture.sub!(/public\//, '') %>" />
  <% end %>
</body>
</html> 
```

我们做了一个小小的 html 页面。在`get`方法中，Erb 被指定为我们的模板语言。另外，请注意视图的名称是一个符号。内联模板要求您创建一个同名的类变量，以便 Sinatra 知道要呈现哪个模板。我们稍后会看到这个。

你认为如果我们进行测试，他们会通过吗？继续运行它。

```
$ ruby test/test.rb 
Run options: --seed 24993

# Running tests:

.F

Finished tests in 0.027037s, 73.9727 tests/s, 110.9591 assertions/s.

  1) Failure:
test_moving_world(MyTest) [test/test.rb:17]:
--- expected
+++ actual
@@ -1 +1,14 @@
-"slideshow_pictures/test.jpg"
+"<!DOCTYPE html>
+<html>
+  <head>
+  <meta charset=\"UTF-8\">
+  <meta name=\"viewport\" content=\"user-scalable=yes, width=device-width\" />
+<title>Moving Pictures</title>
+</head>
+<body>
+  
+    <img src=\"slideshow_pictures/test.jpg\" />
+  
+</body>
+</html>
+"

2 tests, 3 assertions, 1 failures, 0 errors, 0 skips 
```

是的，我的也爆炸了。现在我们输出 HTML，而不仅仅是图片路径。我们应该重写测试来检查主体是否包含“slideshow_pictures/test.jpg”

#### test/test.rb

```
â€¦
def test_for_echo
  get '/'
  assert last_response.ok?
  assert last_response.body.include?("slideshow_pictures/test.jpg")
end
â€¦ 
```

*屏住呼吸*重新运行测试。

```
$ ruby test/test.rb
Run options: --seed 48502

# Running tests:

..

Finished tests in 0.043955s, 45.5011 tests/s, 68.2516 assertions/s.

2 tests, 3 assertions, 0 failures, 0 errors, 0 skips 
```

这应该可以在浏览器中运行。启动 Sinatra 并在[浏览器](http://127.0.0.1:4567/)中查看。现在，整个世界就是一个舞台。

![mp-a-little-husky](img/9d92718be8e6e88494b09b90c9b8071c.png)

## 让我们回顾一下我们所做的。

*   我们学会了从目录中读取文件。
*   我们学习了如何查找特定的文件类型。
*   我们了解了 Sinatra 应用程序的默认文件夹结构。
*   我们学会了设置自己的错误信息。

## 下一步是什么？

你可能会问“这很好，但是我如何把图片上传到幻灯片目录中呢？”这是下一部分。

## 分享这篇文章