# Rails 2.0 特性:多视图

> 原文：<https://www.sitepoint.com/rails-20-features-multiple-views/>

Ruby on Rails 框架的下一个主要版本已经埋下了种子。上个月底，发布了预览版，现在我有机会玩了，我认为是时候介绍一些新特性了。

**多视图**

在 Rails 1.2 版本中，引入了 respond_to 块，这使得提供不同的数据类型(如 XML 或 JSON)变得非常容易。你需要做的就是这样:

```
 def index
    @stories = Story.find :all
    respond to { |format|
        format.html {}
        format.xml {
            render :xml => @stories.to_xml
        }
        format.json {
            render :json => @stories.to_json
        }
    }
end 
```

然后，在 web 浏览器上，如果您附加了文件扩展名(例如/stories/index.xml ),您将获得以所请求的格式交付的内容。您甚至可以通过将 MIME::Type.register 添加到 environment.rb 文件的底部来创建自己的自定义类型。

这种方法的一个问题是，没有办法根据文件扩展名提供不同的 HTML 页面。由于 MIME::Type 解析器的工作方式，添加另一个 MIME 类型为 text/html 的内容处理程序会破坏默认的处理程序，这意味着上面的代码会提供错误的视图。

**输入*Mime::type . register _ alias***

现在，您可以告诉 Rails 用 HTML 响应任意多的文件类型！假设您正在设计一个移动版本和一个 iPhone 版本的站点，您可以通过将以下代码放入新的/config/initializers/mime _ types . Rb 文件来创建两种新格式:

```
 Mime::Type.register_alias "text/html", :iphone
Mime::Type.register_alias "text/html", :mobile 
```

这使得以下情况成为可能:

```
 def index
    @stories = Story.find :all
    respond to { |format|
        format.html {}
        format.xml {
            render :xml => @stories.to_xml
        }
        format.json {
            render :json => @stories.to_json
        }
        format.iphone {
           // Serve up the iPhone version
        }
        format.mobile {
           // Serve up the mobile version
        }
    }
end 
```

当然，在每个 respond_to 块中手工呈现不同的版本并不是很枯燥，因此为所有视图文件创建了一个新的命名约定。您可以根据所提供的格式创建三个不同的版本，例如:index.html.erb、index.iphone.erb 和 index.mobile.erb，而不是调用上面的 index.rhtml 示例中的视图文件。

如果 rails 找到一个匹配的视图，它将提供该视图，如果没有，它将提供缺省的. html.erb 或。rhtml 文件。这使得提供不同版本的网站更加容易。

## 分享这篇文章