# 做一个好的 404 员

> 原文：<https://www.sitepoint.com/being-a-good-little-404er/>

在我发布关于如何添加片段缓存的文章之前，我想分享一下，以防人们还没有看到。我记得不久前在 Rick Olson 的代码中看到过这种技术，所以完全归功于他。

如果 ActiveRecord 找不到具有您请求的 ID 的数据库记录，它将引发一个`RecordNotFound`异常。

```
 def show
  @person = Person.find(params[:id])
end 
```

如果`params[:id]`与数据库记录不匹配，将引发`ActiveRecord::NotFound`异常。使用 ActionController 的`rescue_action_in_public`方法，我们可以捕获这些异常并相应地抛出 404，至少对我来说，这涵盖了大约 99%的用例。

为了扩大这个应用程序，将下面的*受保护的*方法添加到您的`ApplicationController.rb`中

```
 def rescue_action_in_public(e)
  if e.is_a? ActiveRecord::RecordNotFound
    render :file => "#{RAILS_ROOT}/public/404.html",
           :status => '404 Not Found'
  else
    super
  end
end 
```

实际上，我通常将这个`render`调用转移到一个`render_404`方法中，这样您就可以处理 HTML、XML 和任何其他类型的请求。如果需要，它还允许您从控制器子类中调用它。

```
 def render_404
  respond_to do |format|
    format.html { render :file => "#{RAILS_ROOT}/public/404.html", :status => '404 Not Found' }
    format.xml  { render :nothing => true, :status => '404 Not Found' }
  end
  true
end

def rescue_action_in_public(e)
  case e when ActiveRecord::RecordNotFound
    render_404
  else
    super
  end
end 
```

要成为一个优秀的 404 小程序，你需要做的就是确保你的重要数据库调用抛出这个异常，这让我想到了一个重要的问题:从 Rails 1.1 开始，唯一一种引发`RecordNotFound`异常的`find`类型是通过 id 查找(阅读文档中关于`find`方法的第一段)。如果你在做一种不同的发现，你必须自己处理:

```
 def show
  @tag = Tag.find_by_name(params[:name]) or raise ActiveRecord::RecordNotFound
end 
```

同样的事情也适用于`find(:all)`和`find(:first)`(即引发自己的异常)。

如果你想直接抛出 404，你可以这样做:

```
 def show
  @tag = Tag.find_by_name(params[:name]) or (render_404 and return)
end 
```

在 rails-core 列表上有关于添加一个`find!`方法的[讨论，这会使这个方法更加干净。](http://groups.google.com/group/rubyonrails-core/browse_thread/thread/9b79f15be8cea7ab/465c7b2f24b03363)

## 分享这篇文章