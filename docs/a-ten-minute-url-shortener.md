# 十分钟的网址缩写

> 原文：<https://www.sitepoint.com/a-ten-minute-url-shortener/>

在 SitePoint 楼下，99designs 的团队最近改进了它的 URL shortener，以支持一系列新功能。使用辛纳特拉和 T2 的组合，我惊讶于它是如此的简单。

## 我们正在建造的东西

在我开始之前，让我澄清一下:我们并没有像 [bit.ly](http://bit.ly/) 或 [TinyURL](http://tinyurl.com/) 那样建立一个成熟的网址缩短器。相反，我们将接受一个类似于`http://gentle-light-20.heroku.com/khw`的请求(Heroku 会给你一个你自己随机生成的、听起来很平静的名字，用于开发目的)并将用户重定向到另一个 URL。在这个例子中，我们将把用户发送到`https://www.sitepoint.com/?p=26564`，WordPress 将把它重定向到一个更友好的 URL。

我还假设您的系统上安装了以下软件:

*   红宝石
*   宝石包装管理系统
*   RSpec 测试框架
*   饭桶

如果你在这些方面需要帮助，Heroku 的快速入门指南有链接可以帮你。

## 关于基数为 36 的数的一个注记

在上面的例子中，你可能想知道我们如何从`khw`到`26564`。嗯，`khw`是一个以 36 为基数的数字。

如果你熟悉十六进制颜色代码，你就会熟悉以 16 为基数的计数系统。在这里，数字不仅可以是 0 到 9，还可以是 A(对应于 10)，B(对应于 11)，以此类推，直到 F(值为 15)。

以 36 为基数的计数系统扩展了这一思想，因此数字可以是 0 到 9 或 A 到 z 之间的任何数字。下表给出了一些以 36 为基数的数字及其十进制对应物的示例:

| 基数为 36 的数字 | 小数值 |
| nine | nine |
| A | Ten |
| B | Eleven |
| Y | Thirty-four |
| Z | Thirty-five |
| Ten | Thirty-six |
| 公元前 1 年 | Forty-seven thousand and sixty-four |

## 步骤 1:设置您的环境

首先，我们需要安装一些宝石。gem 是 Ruby 库，可以使用`gem`命令安装。我们需要为我们的应用程序安装以下 gem:

*   管理我们的应用程序
*   为了测试我们的应用程序
*   为我们的应用程序提供框架
*   `rack`和`rack-test,`，它们为 Ruby 提供了与 web 服务器的接口

要安装这些 gem，请运行以下命令:

```
$ sudo gem install heroku
Successfully installed rake-0.8.7
Successfully installed mime-types-1.16
⋮
Installing RDoc documentation for json_pure-1.4.6...
Installing RDoc documentation for heroku-1.10.5...
$ sudo gem install sinatra
Successfully installed rack-1.2.1
Successfully installed sinatra-1.0
⋮
Installing RDoc documentation for rack-1.2.1...
Installing RDoc documentation for sinatra-1.0...
$ sudo gem install rspec
**************************************************

Thank you for installing rspec-1.3.0

Please be sure to read History.rdoc and Upgrade.rdoc
for useful information about this release.

**************************************************
Successfully installed rspec-1.3.0
⋮
Could not find main page README.rdoc
$ sudo gem install rack
Successfully installed rack-1.2.1
1 gem installed
Installing ri documentation for rack-1.2.1...
Installing RDoc documentation for rack-1.2.1...
$ sudo gem install rack-test
Successfully installed rack-test-0.5.4
1 gem installed
Installing ri documentation for rack-test-0.5.4...
Installing RDoc documentation for rack-test-0.5.4...
$ 
```

## 步骤 2:编写一些测试

现在我们已经安装了我们的 gem，我们可以开始编写一些 Ruby 代码了。让我们从一些简单的测试开始。创建一个名为`my-url-shortener`的目录。然后，在名为`my-url-shortener-test.rb`的文件中，放入以下内容:

```
require "my-url-shortener"
require "spec"
require "rack/test"

set :environment, :test

describe "My URL Shortener" do
	include Rack::Test::Methods

	def app
		Sinatra::Application
	end

	it "redirects to a blog post" do
		get "/123" # 123 (base 36) == 1371 (base 10)
		last_response.status.should == 301
		last_response.headers["location"].should == "https://www.sitepoint.com/?p=1371"
	end

	it "redirect to blog post with lowercase digits" do
		get "/a" # a (base 36) == 10 (base 10)
		last_response.status.should == 301
		last_response.headers["location"].should == "https://www.sitepoint.com/?p=10"
	end

	it "redirect to blog post with uppercase digits" do
		get "/A" # A (base 36) == 10 (base 10)
		last_response.status.should == 301
		last_response.headers["location"].should == "https://www.sitepoint.com/?p=10"
	end

	it "redirects to home" do
		get "/"
		last_response.status.should == 301
		last_response.headers["location"].should == "https://www.sitepoint.com/"
	end

	it "unrecognised path redirects" do
		get "/foo/bar"
		last_response.status.should == 301
		last_response.headers["location"].should == "https://www.sitepoint.com/foo/bar"
	end
end 
```

在上面的代码中，我们测试了以下重定向:

| 请求的路径 | 重定向目的地 |
| `/123` | `https://www.sitepoint.com/?p=1371` |
| `/A` | `https://www.sitepoint.com/?p=10` |
| `/a` | `https://www.sitepoint.com/?p=10` |
| `/` | `https://www.sitepoint.com/` |
| `/foo/bar` | `https://www.sitepoint.com/foo/bar` |

只有当有一个名为`my-url-shortener`的类时，运行这些测试才会起作用，所以让我们创建它。Sinatra 会为我们做所有的工作，只要我们用下面的代码行创建一个名为`my-url-shortener.rb`的文件:

```
require "sinatra" 
```

现在我们可以通过在命令行输入`spec my-url-shortener-test.rb`来运行测试:

```
$ spec my-url-shortener-test.rb
FFFFF

1)
'My URL Shortener redirects to a blog post' FAILED
expected: 301,
     got: 404 (using ==)
./my-url-shortener-test.rb:16:

2)
'My URL Shortener redirect to blog post with lowercase digits' FAILED
expected: 301,
     got: 404 (using ==)
./my-url-shortener-test.rb:22:

3)
'My URL Shortener redirect to blog post with uppercase digits' FAILED
expected: 301,
     got: 404 (using ==)
./my-url-shortener-test.rb:28:

4)
'My URL Shortener redirects to home' FAILED
expected: 301,
     got: 404 (using ==)
./my-url-shortener-test.rb:34:

5)
'My URL Shortener unrecognised path redirects' FAILED
expected: 301,
     got: 404 (using ==)
./my-url-shortener-test.rb:40:

Finished in 0.020694 seconds

5 examples, 5 failures
```

这里我们可以看到五个测试都失败了，我们的重定向器只返回 404s。让我们做点什么，好吗？

## 步骤 3:编写应用程序

将以下内容添加到`my-url-shortener.rb`:

```
get %r{^/([0-9a-zA-Z]+)$} do
	postid = params[:captures][0].to_i(36)
	redirect "https://www.sitepoint.com/?p=#{postid}", 301
end 
```

这四行:

*   拦截任何匹配正则表达式`^/([0-9a-zA-Z]+)$`的请求(即任何以斜杠开头后跟一个或多个字母数字字符的请求)，
*   从传入的请求中提取一个 Post ID，将一个基数为 36 的数字转换成一个整数，
*   将用户重定向到`https://www.sitepoint.com/?p=postid`，然后
*   停止处理此请求

重新运行测试表明我们已经取得了一些进展:

```
$ spec my-url-shortener-test.rb
...FF

1)
'My URL Shortener redirects to home' FAILED
expected: 301,
     got: 404 (using ==)
./my-url-shortener-test.rb:34:

2)
'My URL Shortener unrecognised path redirects' FAILED
expected: 301,
     got: 404 (using ==)
./my-url-shortener-test.rb:40:

Finished in 0.0337120000000001 seconds

5 examples, 3 failures
```

这些结果的第一行中的三个点表明我们的三个测试通过了。剩下的两种情况可以通过在`my-url-shortener.rb`的末尾添加一个 catchall 来处理:

```
get "/*" do
	path = params["splat"]
	redirect "https://www.sitepoint.com/#{path}", 301
end 
```

现在让我们试着运行测试:

```
$ spec my-url-shortener-test.rb
.....

Finished in 0.008923 seconds

5 examples, 0 failures
```

成功！

现在，让我们把这玩意部署到赫罗库。

## 步骤 4:部署到 Heroku

为了部署到 Heroku，我们需要设置两个配置文件:一个名为`.gems,`的文件包含应用程序需要的 gem 列表，另一个名为`config.ru`的文件告诉 Heroku 如何运行我们的应用程序。这些文件和这个项目中的其他文件一样，都非常简单。

将此内容放入`.gems`:

```
sinatra
```

将此内容放入`config.ru`:

```
require "my-url-shortener"
run Sinatra::Application
```

现在我们已经准备好部署我们的应用程序了。如果你还没有这样做，请访问 Heroku 并注册。一旦你完成了这些，请按照 [Heroku 的快速入门指南](http://docs.heroku.com/quickstart)中的说明创建一个基本的 Heroku 应用程序。

在麦克 OS X，这可以归结为:

```
$ cd ~/my-url-shortener/
$ git init
Initialized empty Git repository in /Users/craiga/my-url-shortener/.git/
$ git add .
$ git commit -m "Creating URL shortener application"
[master (root-commit) b602ee0] Creating URL shortener application
 4 files changed, 57 insertions(+), 0 deletions(-)
 create mode 100644 .gems
 create mode 100644 config.ru
 create mode 100644 my-url-shortener-test.rb
 create mode 100644 my-url-shortener.rb
$ heroku create
Enter your Heroku credentials.
Email: craiga@sitepoint.com
Password:
Uploading ssh public key /Users/craiga/.ssh/id_rsa.pub
Creating gentle-light-20... done
Created http://gentle-light-20.heroku.com/ | git@heroku.com:gentle-light-20.git
Git remote heroku added
$ git push heroku master
Counting objects: 6, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (6/6), 940 bytes, done.
Total 6 (delta 0), reused 0 (delta 0)

-----> Heroku receiving push
-----> Sinatra app detected

-----> Installing gem sinatra from http://rubygems.org
       Successfully installed sinatra-1.0
       1 gem installed

       Compiled slug size is 236K
-----> Launching.... done
       http://gentle-light-20.heroku.com deployed to Heroku

To git@heroku.com:gentle-light-20.git
 * [new branch]      master -> master
```

嘣！您的应用程序已经部署到 Heroku。通过访问 Heroku 给你的 URL 来检查一下(在上面的例子中，是`http://gentle-light-20.heroku.com`)。

## 第五步:没有第五步！

仅此而已。如果你想使用一个短域名，比如我们在 99designs 使用的 99d.me 域名，你可以使用 Heroku 网站上的工具将其分配到你的网站上。在撰写本文时，你需要输入你的信用卡号，但是如果你需要大量的数据库空间或者更好的性能，就需要付费。

## 分享这篇文章