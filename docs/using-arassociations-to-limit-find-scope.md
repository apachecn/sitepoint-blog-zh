# 使用 AR::Associations 限制 find()范围

> 原文：<https://www.sitepoint.com/using-arassociations-to-limit-find-scope/>

在我目前正在开发的一个电子商务应用程序中，有一个用于销毁用户购物车中一行商品的 URL:
`http://127.0.0.1:3000/cart/line-item/16/destroy`

像所有优秀的 web 编码猴子应该做的那样，我需要在销毁 id 为 16 的 LineItem 之前检查它是否确实为用户所有。听起来很明显？可怕的是，我见过很多 web 应用程序不做这种检查，通常只是假设通过 GET 或 POST 传递的数据库 id 是有效的。

编写`LineItemsController`的`destroy`动作的初始方法可以是:

```
 def destroy
  fetch_order_from_session # sets up the @order object
  @line_item = LineItem.find(params[:id])
  if @order.line_items.include?(@line_item)
    @line_item.destroy
  end
end 
```

另一种方式可能是:

```
 def destroy
  fetch_order_from_session
  @line_item = LineItem.find(:first, :conditions => ['id = ? AND order_id = ?', params[:id], @order.id])
  @line_item.destroy
end 
```

这两个都可以用，但是不太好看。ActiveRecord 关联的前提之一是，如果您已经定义了两个模型对象之间的关系，那么您就不需要通过手工编写外键搜索来重复自己的工作。

一个不太为人所知(也很少被记录)的技巧是使用协会的`find`方法:

```
 def destroy
  fetch_order_from_session
  @line_item = @order.line_items.find(params[:id])
  @line_item.destroy
end 
```

关联的`find`方法与常规 find 完全一样，但是 SQL select 自动作用于父对象的外键。在这种情况下,`"order_id = #{@order.id}"`被添加到 SQL SELECT 子句中。您也不必止步于简单的查找，您可以执行所有常规的查找技巧:

```
 @order.line_items.find(:all, :conditions => ["quantity >= ?", 0]) 
```

如果您有一个由`before_filter`创建的用户对象，这种技术也很有用。不必将`user_id = @current_user.id`添加到每个查找中，只需通过`@current_user`对象的关联直接执行查找。如果你是使用联想的新手，我建议你看看我以前的一篇博文:[滥用错误..使用关联](http://toolmantim.com/article/2006/2/15/abusing_err_using_ar_associations)。

顺便提一下，不要忘记使用 [ActionController 的验证宏](http://api.rubyonrails.com/classes/ActionController/Verification/ClassMethods.html)来确保这是一个 POST 请求。你不会希望那些疯狂的鸽子搞乱你可爱的数据库吧？

## 分享这篇文章