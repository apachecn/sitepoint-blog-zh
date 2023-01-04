# 你的防御有多深？

> 原文：<https://www.sitepoint.com/how-deep-are-your-defenses/>

至此，您已经构建了安全的 web 应用程序。您已经启用了 ASP。NET 的便捷[认证](http://msdn2.microsoft.com/en-US/library/eeyk640h.aspx)和[授权](http://msdn2.microsoft.com/en-US/library/eeyk640h.aspx)特性。但是你做得够多了吗？不，一点也不。如果您忘记部署 web.config 来控制对应用程序管理文件夹的访问，会发生什么情况？或者，如果攻击者通过破坏您的数据库并引用您的业务逻辑层来获得对机器的访问权限，该怎么办？或者，如果攻击者发现了一个 SQL 注入并开始直接写入数据库呢？在许多情况下，简单的答案是“坏事”经常导致失业。

但是对于攻击者来说，这并不容易。有许多策略可以用来扩展 web 界面之外的安全性。像一支优秀的军队一样，为了保护应用程序，您必须练习深度防御。

如上所述，强化表面 easy—ASP.NET 提供了一些非常健壮和灵活的身份验证和授权特性。在 2.0 版本中，它甚至在[成员资格提供者](http://msdn2.microsoft.com/en-us/library/sx3h274z.aspx)和[角色提供者](http://msdn2.microsoft.com/en-us/library/6b241xwt.aspx)中提供了一些有限的端到端解决方案。但这只是保护了表面——就像一个蛋壳，你的应用程序在外面很坚硬，但很容易穿透外壳。与其依赖外壳来提供安全性，不如实现深度防御来减轻外壳被破解的任何影响。

**深入业务逻辑**

毫无疑问，保护业务逻辑层最简单的方法是利用。NET 框架的安全特性，特别是[principal permission attribute](http://msdn2.microsoft.com/en-us/library/system.security.permissions.principalpermissionattribute.aspx)。此属性可应用于任何类、方法和属性。如果主体权限检查失败，将抛出一个[安全异常](http://msdn.microsoft.com/library/default.asp?url=/library/en-us/cpref/html/frlrfsystemsecuritysecurityexceptionclasstopic.asp)。例如，假设您有一个未经身份验证的用户不能访问的客户对象。您可以简单地用主体权限来修饰对象，如下所示:

```
 [PrincipalPermission(SecurityAction.Demand, Authenticated=true)]
public class Customer
{
	//code for your customer object.
} 

```

或者，假设您有一个应用程序，其中的需求声明只有管理员才能将用户添加到角色中。封装在 AddRole(string)方法中的功能。您可以使用如下代码确保任何用户都不能将自己添加到 Admins 组中:

```
 [PrincipalPermission(SecurityAction.Demand, Role="Admins")]
public void AddRole(string role)
{
	//code to add the role
} 

```

除了提供一种简单的方法来支持所有的安全性， [IPrincipal 接口](http://msdn2.microsoft.com/en-us/library/system.security.principal.iprincipal.aspx)对于日志记录和审计用户操作等情况非常有用。只需在必要的地方捕获对主体对象的引用，并使用 IPrincipal。标识。命名属性以捕获谁在对什么做什么。

使用上述技术将安全性嵌入到您的业务逻辑库中还有一个巨大的优势:安全性变得更加可测试。测试一个 web 界面通常是一项手工工作，然而有许多单元测试框架可用于。NET——如 [NUnit](http://www.nunit.org/) 或[MbUnit](http://mbunit.tigris.org/)——允许非常快速和容易地测试原则许可。你需要做的就是更换系统。使用正确构造的 GenericPrincipal 处理 threading . thread . current principal，并根据需要查找 SecurityExceptions。而且，有了 NUnit GUI，你甚至可以给老板拍一张漂亮的截图来证明。

**超越业务逻辑，进入数据**

大多数 web 应用程序的最后一道防线在于底层数据存储，通常是某种 RDBMS。对于 ASP.NET，通常情况下，数据存储是一个 Sql 服务器。历史上的一个意外是，Sql Server 能够非常非常安全，但常常由于管理和开发人员的失误而导致闸门大开。而且，特别是对于 Sql Server 2000，这可能会导致非常非常糟糕的事情。看看[这段视频](http://www.rockyh.net/AssemblyHijacking/AssemblyHijacking_media/AssemblyHijacking.wmv) [56k 当心],了解错误配置的 Sql Server 安装和糟糕编码的网站会给你带来什么。

就保护 Sql Server 而言，安装时需要完成几个基本步骤。主要的一点是确保数据库不在本地系统上下文中运行，而是作为一个受限的用户帐户运行。这限制了在最坏情况下可能造成的损害。但是这项任务常常超出了开发人员的职责范围，因此不能完全依赖。

从应用程序开发的角度来看，可以做很多事情来防止 Sql 注入场景在您的 ASP.NET 应用程序中发生。首先，确保限制您的 web 应用程序的用户帐户。绝对不会出现 web 应用程序的用户帐户需要 db_owner 权限的情况；如果需要对所有表进行完全读/写访问，请使用 db_datareader 和 db _ data writer——尽管有人可能会认为所有的插入/更新/删除都应该通过存储过程进行。但是在我所见过的任何情况下，给 web 应用程序权限来控制对数据库的访问都是不合理的。更不用说一些纯粹是开发人员的懒惰要求 web 应用程序完全以 sysadmin 权限运行的场景了。

第二，也可能是更重要的，应该总是利用 ADO.NET 参数将数据传递给数据库。例如，这是不好的:

```
 public void badSql(SqlConnection conn, string input)
{
	string sql = "SELECT * FROM Beers WHERE Type='{0}'";
	SqlCommand cmd = conn.CreateCommand();
	cmd.CommandText = string.Format(sql, input);
	//execute command
} 

```

同样的方法应该这样编码:

```
 public void goodSql(SqlConnection conn, string input)
{
	string sql = "SELECT * FROM Beers WHERE Type=@Type";
	SqlParameter param = new SqlParameter("@Type", SqlDbType.VarChar);
	param.Value = input;
	DbCommand cmd = conn.CreateCommand();
	cmd.CommandText = sql;
	cmd.Parameters.Add(param);
	//execute command   
} 

```

除了防范 Sql 注入之外，使用参数化语句更容易被数据库服务器消化和缓存。

最后，还有一个保护数据库的选择:使用单独的安全上下文进行访问。例如，我开发了许多有三个前端的应用程序:一个公共 web 应用程序，它需要选择很多，很少插入。一个管理网站的应用程序，需要完全访问数据库。最后是一个 web 服务应用程序，它需要做大量的选择和插入工作，但是方式有限。在数据库服务器中，我为每个应用程序分配了不同的角色，并允许每个应用程序访问他们需要的存储过程和表。这意味着，如果我公开了一个不应该公开的数据访问方法，它仍然会失败，因为它不能访问数据存储。

走到这一步？首先，从端到端保护您的 web 应用程序。第二，记得[踢这个岗位](http://www.dotnetkicks.com)。最后，请务必查看 Scott Guthrie 发布的[便捷的 ASP.NET 安全资源](http://weblogs.asp.net/scottgu/archive/2006/09/30/Tip_2F00_Trick_3A00_-Guard-Against-SQL-Injection-Attacks.aspx)(向下滚动到项目列表)。

## 分享这篇文章