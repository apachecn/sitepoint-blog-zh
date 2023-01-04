# 为 Rails 2.0 做准备:基于控制器的异常处理

> 原文：<https://www.sitepoint.com/preparing-for-rails-20-controller-based-exception-handling/>

由于 Ruby 是一种纯面向对象的语言，异常在控制流程中扮演着重要的角色。以前，您可以选择在本地级别拯救异常，也可以在控制器中覆盖 rescue_action 方法。

前一种方法为您提供了在异常情况下做什么的细粒度控制:

```
 begin
    user.save!
rescue ActiveRecord::RecordInvalid
    render :action => 'new'
end 
```

在这种情况下，如果引发 ActiveRecord::RecordInvalid 异常(保存！如果验证失败，方法将引发此问题)Rails 将呈现“新建”动作。很明显，尽管在相同的方法周围添加 begin/rescues 非常耗时，而且不是很枯燥——这就是 rescue_action 变得有用的地方:

```
def rescue_action(exception)
  if exception == ActionView::TemplateError
    render :template => 'errors/404'
  else
    super
  end
end

```

这个(相当做作的)示例将捕获任何 ActionView::TemplateError 并呈现/app/views/errors 目录中的 404.erb 文件。您可以将该方法放入任何控制器(包括 app_controller ),但同样，设置它们需要做大量的工作，确保每个控制器针对给定的异常执行正确的操作，这也是 Rails 2.0 引入 rescue_from 的原因。

rescue_from 是每个控制器的一个属性，它允许您定义一个在调用特定异常时运行的方法。所以上面的例子会变成:

```
 rescue_from ActionView::TemplateError, :with => :render_404

def render_404(exception)
   render :template => 'errors/404'
end 

```

或者如果您喜欢使用内嵌块:

```
 rescue_from ActionView::TemplateError do { render :template => 'errors/404' } 

```

在当前的 PR 版本中，rescue_from 技术只能捕获精确类型的异常，因此它不会捕获子类异常——如果您有一个扩展 ActionView::TemplateError 的名为 MyTemplateError 的自定义异常，上面的代码将不起作用。好消息是 edge rails 的一个补丁修复了这个问题，所以 Rails 2.0 的发布版本将按预期工作。

## 分享这篇文章