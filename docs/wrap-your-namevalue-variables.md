# 包装您的名称值变量

> 原文：<https://www.sitepoint.com/wrap-your-namevalue-variables/>

ASP.NET 为开发人员提供了许多松散类型的键值集合，根据持久性需要，可以在其中存放变量。短列表包括视图状态、会话、应用程序和 HttpContext.Items。当需要在单个请求之外保存一个对象或者在 http 管道的不同部分之间穿梭时，这些集合会非常方便。但是这是有代价的——这些集合是松散类型的，只是返回对象。此外，没有编译时检查来确保您在正确的位置请求了正确的键。错误在最好的情况下会导致崩溃，在最坏的情况下会导致有趣的数据损坏。

但是这些问题可以很容易地通过预先的一点训练来避免。你看，不直接调用这些变量，很有可能将它们包装在一个适当范围的属性中，并管理对底层数据的访问。对于示例，我们将使用会话，但语义将与上述任何集合保持相同。

所以，假设我们有一个小小的网上商店。这个网络商店有一个购物车。我们将在会话中隐藏这个购物车对象。现在，我们可以看到许多这样的代码:

```
 ShoppingCart sc=(ShoppingCart)Session[“ShoppingCart”];
If (sc==null)
{
      sc=new ShoppingCart();
}
//do stuff with ShoppingCart. 

```

或者，在某个基础页面类中，您可以定义如下内容:

```
 protected ShoppingCart Cart
{
    get
    {
        if (Session["ShoppingCart"] == null)
        {
            Session["ShoppingCart"] = new ShoppingCart();
        }
        return (ShoppingCart)Session["ShoppingCart"];
    }
} 

```

这种方法的优点是，调用代码甚至不需要担心购物车存储在哪里/如何存储，或者它是否可以为空，或者如何将它从底层存储中丢弃。此外，如果在将来的某一天，您决定将这个购物车存储在视图状态中会更好，那么更改非常非常容易，因为它只需要在一个地方进行更新。

享受而不要害怕 [![kick it on DotNetKicks.com](img/58a2cf836165a3429ef414441f1a5b77.png)](https://www.dotnetkicks.com/kick/?url=https://www.sitepoint.com/wrap-your-namevalue-variables/) 。

## 分享这篇文章