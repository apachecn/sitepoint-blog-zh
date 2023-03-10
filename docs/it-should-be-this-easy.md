# 应该很容易

> 原文：<https://www.sitepoint.com/it-should-be-this-easy/>

*…发帖人* [大卫·梅德洛克](https://www.sitepoint.com/forums/member.php?u=24385) *:*

说到数据库，有时我们只是简单地利用我们已有的东西，并希望它在未来的某个时候会变得更好。例如，有时您可能会发现自己只能使用 Microsoft Access。现在，Access 不是为网站设计的。它在小型网站上运行良好，但是很快就变得难以管理，有几个方面:

*   当通过网站访问 Access 数据库时，它会被锁定，因此如果您需要将更改上传到数据库，几乎不可能进行覆盖。
*   如果用户与你的网站交互，你必须确保在对数据库进行结构性修改时不会丢失任何数据。这意味着在您的开发机器上进行更改，从您的网站下载当前版本，并梳理数据库以确保您添加并修改了所有正确的列和表，然后确保数据成立，然后打破 Access 文件的锁定(通常通过更改 DSN)，然后上传新的数据库……(是的，很乱。)
*   Access 数据库所能支持的并发用户数量非常有限，一旦达到一定的规模，就会出现严重的性能问题。

因此，从某种意义上说，对于许多快速发展的网站来说，转移到实际的数据库服务器是必要的。对于 Access 用户，通常选择 SQL Server，因为使用数据转换服务在 Access 和 SQL Server 之间移动数据非常容易。

现在，在周末，我发现自己需要将一个网站的数据库从 Access 转移到 SQL Server。这比我想象的要容易。我要做的就是:
[list = 1][*]创建 SQL Server 数据库:一个用于测试，一个用于生产。[*]创建 ODBC 数据源[*]将数据从 Access 导出到测试数据库[*]在我的 application.cfm 文件中更改我的 DSN 变量[*]将我的查询中的任何 Now()函数更改为 GetDate()函数。[*]在站点的子域上测试代码[*]上传新代码[/list]总共可能需要两三个小时，因为我必须找到一个可以在远程主机上使用的[企业管理器](https://www.sitepoint.com/forums/showthread.php?t=120875&highlight=enterprise+manager)的副本。幸运的是，对于我们这些永远破产的人来说，您可以下载 SQL Server 的试用版，并获得一个永不过期的企业管理器版本。(参见上面引用的线程。)一旦我有了这个，其他的一切都是小事一桩。:)

所以，实际上，我必须做的唯一应用程序级别的更改是我的查询中的 DSN 名称和当前日期函数。当前日期函数可以用几种方法解决:

1.通过将一个名为“NowFunction”(或其他名称)的变量设置为当前数据库所需的函数名:

`[left]INSERT INTO myTable ( date_added ) VALUES ( #NowFunction# )[/left]` 

2.通过使用 ColdFusion 函数 Now()插入日期:

 `<cfquery name="test" data-source="#myDSN#">INSERT INTO myTable ( date_added ) VALUES ( #Now()# )</cfquery>` 

并且**始终**确保在部署之前，在测试环境**中全面测试一切。这就是我要说的。目前…**

(注意:上面的代码块还不能正常工作。我会让 SP 的人知道，这样他们就可以检查一下。)

## 分享这篇文章