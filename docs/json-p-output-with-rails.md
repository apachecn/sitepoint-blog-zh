# 使用 Rails 的 JSON-P 输出

> 原文：<https://www.sitepoint.com/json-p-output-with-rails/>

在最近的 [Web Directions 06 会议](http://webdirections.org)上，Cameron Adams 和 Sitepoint 的 Kevin Yank 给[做了一个关于 Mashups 和 API 的演讲](http://www.themaninblue.com/writing/perspective/2006/10/03/)，其中提到了 JSON-P

JSON-P 是一种包装 API 调用的 JSON 输出的方法，允许其他开发人员绕过浏览器安全机制，从他们的页面中调用您的 API。

如果您查看一下 [Web Connections](http://connections.webdirections.org) 的 HTML 源代码，您会注意到我们在内部使用 JSON-P 来提供 JS 数据以进行映射。在 JSON-P 调用的 URL 中，我们可以指定参数`variable`、`callback`或两者都指定，这将允许其他人在他们自己的 mashups 中使用我们的 JSON。

它还很好地集成了新的 Rails REST 功能。比如[杰瑞米·基思的个人资料页面](http://connections.webdirections.org/people/37)也可以通过[插播以 JSON 格式输出。结尾](http://connections.webdirections.org/people/37.js)的 js’。这很好，但是如果您想从另一个页面调用它，这不是很有用。

假设杰里米想在自己的网站上混搭这些数据，以显示他和谁是朋友。他需要某种 JSON-P 输出，这样他就可以从一个`script`标签访问数据。

要添加 JSON-P“padding ”,只需指定一个“variable”参数，该参数声明您希望将输出分配给的 Javascript 变量的名称。在本例中，我们将[指定“变量=人员 JSON”](http://connections.webdirections.org/people/37.js?variable=personJSON)。注意到输出的不同了吗？现在尝试指定`callback`，或者同时指定`callback`和`variable`。

那么这是如何在幕后实现的呢？我有一个助手函数，我在整个应用程序中使用它来输出 JSON:

```
 class ApplicationController < ActionController::Base
  protected
    def render_json(json, options={})
      callback, variable = params[:callback], params[:variable]
      response = begin
        if callback && variable
          "var #{variable} = #{json};n#{callback}(#{variable});"
        elsif variable
          "var #{variable} = #{json};"
        elsif callback
          "#{callback}(#{json});"
        else
          json
        end
      end
      render({:content_type => :js, :text => response}.merge(options))
    end
end 
```

然后我可以很简单地在我的`PeopleController#show`动作中使用它:

```
 class PeopleController < ApplicationController
  def show
    @person = Person.find(params[:id])
    respond_to do |format|
      format.js { render_json @person.to_json }
    end
  end
end
```

这就是全部了。使用 Rails 的 JSON-P 输出。

## 分享这篇文章