# 在 Global.asax 中设置全局对象

> 原文：<https://www.sitepoint.com/setting-global-objects-in-globalasax/>

来自论坛请求:)

ASP.NET 包括一个 Global.asax 文件，该文件公开了许多应用程序范围内的事件，您可以使用这些事件来控制应用程序的执行。

它还允许您定义全局范围的对象。然而，需要注意的是……由于 ASP.NET 页面中的每个页面都在自己的线程中运行，你需要确保你的对象对于请求你站点的多个用户是线程安全的。

为什么这很重要？假设有两个请求，分别来自用户 A 和用户 B。每个请求运行在一个单独的线程中，用户 A 在一个线程中操作一个全局对象，而用户 B 在另一个线程中操作同一个对象。根据 Windows 的不同，这些可能会以任何顺序执行…这意味着您的全局对象不会为两个用户同步。

因此，为了避免这种复杂性，使用线程安全的对象，或者，特别感兴趣的是，使用哈希表作为变量的线程安全容器:

 `using System;
using System.Collections;`

 `命名空间 MyApp。集合{

公共类 globalVariableStore{
私有哈希表 myCollection
私有哈希表 threadSafeCollection

public globalVariableStore()
{
my collection = new Hashtable()；
threadSafeCollection = Hashtable。已同步(my collection)；
}

public void addVariable(字符串名，对象值)
{
threadSafeCollection[name]= value；
}` 

`public object retrieveVariable(string name) {
return threadSafeCollection[name];
}
}
}`

然后，通过在 global.asax 文件中添加一行，可以将 globalVariableStore 的实例定义为全局范围的:

然而，创建你自己的全局对象可以给全局定义的对象实例更大的深度和控制…只要确保它们是线程安全的！

**持久存储**

还有一种机制是将字符串值添加到您的 web.config 文件中，然后这些字符串值总是可供您的应用程序使用:

 `<appsettings><add key="connectionString" value="foo"></add></appsettings>` 

这应该放在文件中的“configuration”元素下。

然后您可以这样检索该值:

 `string connectionString = ConfigurationSettings.AppSettings("connectionString");`

记住，web.config 文件不能通过您的服务器提供给浏览器，所以这提供了一些安全性，但是有价值的信息以纯文本的形式存储在您的服务器上，所以预先加密一些敏感的字符串可能是个好主意！

## 分享这篇文章