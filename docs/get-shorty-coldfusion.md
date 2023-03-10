# 简而言之:用 ColdFusion 修剪脂肪

> 原文：<https://www.sitepoint.com/get-shorty-coldfusion/>

厌倦了 TinyURL、tr.im 或 bit.ly 这些老掉牙的网址缩写？曾经希望你可以运行你自己的网址缩写？在 ColdFusion 中，这可能比您想象的要容易。为了展示 ColdFusion 的强大和易用性，我们将从头开始构建我们自己的简单 URL shortener。在本文中，我们将从一组基本的功能开始——创建缩短的链接，并扩展它们。当你读完之后，不要忘记[试试这个小测验](https://www.sitepoint.com/quiz/adobe/get-shorty-coldfusion/)！

为了跟进，您需要[下载并安装 ColdFusion 9 和 ColdFusion Builder 测试版](http://labs.adobe.com/)并将其安装在您的开发机器上。ColdFusion 9 也适用于 Linux，并且可以安装 cold fusion Builder—[,详见复合理论教程。](http://www.compoundtheory.com/?action=displayPost&ID=414)你还需要[获取本教程的代码档案](https://i2.sitepoint.com/examples/getshorty/SitePointURLShortener.zip)。

##### 开始之前

本教程假设您使用独立的内置开发服务器，在本地主机的端口 8500 上运行 ColdFusion 9 beta。如果您已经在 localhost 以外的机器上安装了 ColdFusion，或者正在使用 IIS 或 Apache 而不是独立的开发服务器，或者正在使用 J2EE 服务器配置，您将需要更改代码清单中提到的服务器路径。

我们还假设您在同一台本地主机上安装了 ColdFusion Builder。如果你已经在你的机器上安装了 Eclipse IDE(包括 Flash Builder 或 Flex Builder)并且已经安装了 ColdFusion Builder 插件，你需要确保你在 ColdFusion 透视图中:从窗口菜单中选择**打开透视图>其他> ColdFusion** 。

在我们开始编码之前，我们将告诉 ColdFusion Builder 我们将使用哪个 ColdFusion 服务器。我们将使用 ColdFusion Builder 窗口底部的**服务器**选项卡进行配置。这里有相当多的步骤要遵循，我在下面为你进行了分解:

1.  点击**添加服务器**图标——上面有一个小小的黄色加号。

3.  选择 **ColdFusion** 作为服务器类型，点击**下一步**。

5.  为服务器输入一个名称，以便您可以识别。我已经进入了本地主机。

7.  输入主机名–如果您运行的是默认设置，它也是本地主机。

9.  输入端口–内置开发设置的默认端口是 8500。

11.  输入 RDS 用户名，该用户名始终为 admin，除非您在安装服务器时指定了不同的用户名(通常只有在 J2EE 服务器配置上运行时才会出现这种情况)。

13.  输入安装 ColdFusion 时指定的 RDS 密码。点击**下一个**。

15.  浏览您的服务器主目录。这是 ColdFusion 的安装目录–在 Windows 上，它可能是 C:cold fusion 9；在 Mac 上，/Applications/ColdFusion 9。

17.  您可能还需要浏览您的文档根目录。这是您的 web 根目录–在默认的 Windows 安装中，它可能是 C:cold fusion 9 wwwroot；在 Mac 上，它位于/Applications/cold fusion 9/wwwroot。

19.  从下拉框中选择安装的 ColdFusion 版本–9.0 . x。

21.  点击**完成**。

ColdFusion Builder 将验证这些详细信息并连接到服务器。如果一切顺利，您应该在 **Servers** 选项卡中看到该服务器。您可以从该选项卡启动和停止服务器，并访问其 ColdFusion 管理员面板。

![The Servers tab, showing our local server](img/ed829239501ac18c850826b92abdd4f1.png)

ColdFusion Builder 用于项目。我们现在可以创建一个新项目，并将其与我们的开发服务器相关联，但是为了让您更容易，我们已经将所有代码包含在一个可下载的项目中，我们现在将导入该项目。

1.  下载示例 zip 文件，并将文件夹解压缩到 ColdFusion web 根目录。如果需要，现在将文件夹的名称更改为 URLS。

3.  屏幕左侧的导航面板显示了当前项目中的文件。在**导航器**中点击右键，选择**导入**。

5.  从导入向导，向下钻取到 **ColdFusion - >导入现有项目**，点击**下一步**。

7.  浏览刚刚提取的文件夹。

ColdFusion Builder 将扫描文件夹并查找任何项目文件，这些文件将列在下面的**项目列表**中。选中我们的 URLs 项目旁边的框，然后点击**完成**。

![Selecting the project in the Import ColdFUsion Project dialog](img/0c3ad3b6680917fb4c61de5ff787feee.png)

该项目现在将列在您的**导航器**面板中。最后一步是将项目与我们的服务器关联起来。

1.  右击项目名称，选择**属性**。

3.  从对话框左侧选择 **ColdFusion 服务器设置**。

5.  从服务器下拉框中选择您的本地开发服务器，并点击 **OK** 。

![Selecting a server](img/2247f973fe7271b7fdc308cfb1ce5187.png)

##### 设置数据库

我们的网址缩写应用程序将需要一个数据库。Apache Derby 是一个开源数据库，具有一些相当强大的功能，它内置于 ColdFusion 8 和更高版本中。对于我们的目的来说，这是完美的，因为尽管 Derby 没有客户机和用户界面，但是建立数据库非常简单，不需要安装 MSSQL、MySQL 或其他数据库服务器。

ColdFusion 使用数据源名称来存储数据库连接。数据源名称在 ColdFusion Administrator 中设置，它是 ColdFusion 服务器的命令中心。要访问本地开发服务器的 ColdFusion Administrator，请右键单击 **Servers** 选项卡中的服务器名称(记住，它在屏幕底部)，然后选择**启动管理页面。**

ColdFusion Administrator 在新的 Eclipse 选项卡中启动，并提示您输入管理员密码(即您在安装过程中选择的密码)。

登录后，从左侧菜单中选择数据源。在屏幕的顶部有一个创建新数据源的表单。输入“URLShortener”作为数据源名称，从驱动程序下拉列表中选择 Apache Derby Embedded，然后单击 **Add** 。

你就快到了！在您现在看到的表单中，在数据库文件夹(与数据源名称相同)中键入“URLShortener”，并勾选**创建数据库**复选框。点击**提交**，您的 Derby 嵌入式数据库将与您的 ColdFusion 数据源名称同时创建。

![Setting up our Derby database](img/e58f6dcec95081c45bd7596e10d19399.png)

##### 问题概述

你还和我在一起吗？现在我们有了一个开发环境和一个数据库，我们准备考虑如何开发我们的 URL shortener。

URL 缩短应用程序现在遍布互联网。URL 缩写旨在解决在即时消息、Twitter 消息或任何其他形式的有限字符通信中尝试使用长 URL 时遇到的问题。URL shortener 的基本前提是给你一个到他们网站的唯一的短 URL，当点击它时，会将用户重定向到更长的 URL。

例如，使用以下 URL 链接到 SitePoint 文章“ColdFusion 9 中的新特性”:

[https://www.sitepoint.com/article/whats-new-coldfusion-9/4/](https://www.sitepoint.com/whats-new-coldfusion-9/)

服务 TweetBurner 会给你一个更短的网址:

[http://twurl.nl/qiakd3](http://twurl.nl/qiakd3)

缩短后的网址只有 22 个字符，还不到原来 58 个字符链接长度的一半。而且有了更长的 URL，好处就更明显了。

##### 向数据库添加表

为了创建我们的 URL 缩短应用程序，我们需要在 Derby 数据库中创建一个表。我们将把我们的表称为`content`，并给它以下几列:

| 圆柱 | 数据类型 | 描述 |
| 身份证明（identification） | 整数 | 数据 |
| 标签 | 字符(100) | – |
| 文本 | 文本 | – |
| 环 | 文本 | 我们正在链接的 URL |
| 快捷链接 | 字符(20) | 将识别我们的网址的短代码 |

这个数据库中有一些字段对于一个简单的 URL 缩写程序来说并不是绝对必要的，但是我们将在本教程的后续文章中使用它们，所以我们不妨现在就添加它们。

正如我们之前提到的，Apache Derby 数据库的嵌入式版本没有客户端或用户界面，因此为了创建我们的表，我们将在 ColdFusion 中运行 SQL 脚本。在 ColdFusion Builder 项目的 web 根目录下打开文件 db.cfm。它包含以下代码:

`Creating database...  

<cfquery datasource="URLShortener" result="return">  
 CREATE TABLE content  
 (  
   id INTEGER NOT NULL GENERATED ALWAYS  
      AS IDENTITY (START WITH 1, INCREMENT BY 1),  
   label VARCHAR(100),  
   text VARCHAR(500),  
   link VARCHAR(500) NOT NULL,  
   shortlink VARCHAR(20) NOT NULL,  
   CONSTRAINT shortlink_uc UNIQUE(shortlink)  
 )  
</cfquery>  

<cfdump var="#return#">` 

您可以通过点击工具栏中的 **Run** 图标来执行这段代码——它是一个绿色圆圈中的白色箭头。这将在您的默认浏览器中启动脚本。也可以通过切换到编辑窗口底部的选项卡来启动 ColdFusion Builder 中的浏览器。在您的计算机上检测到的每个浏览器都有一个选项卡。

执行之后，您将看到我们运行的查询操作的结果:

![The result of our database creation script](img/833fae777c78263517cb627df33c40d2.png)

虽然没有什么可以具体说明操作是成功的，但是它表明没有错误，并且显示了执行时间和生成的运行的 SQL 代码，所以我们可以放心地假设一切正常。

##### 应用程序代码

看看 ColdFusion Builder 项目的内部，您会看到其他几个项目。cfm 文件和几个。cfc 文件–一个在根目录下，一个在/com 子目录下。文件结尾为。cfm 是常规的 ColdFusion 模板文件。的。cfm 扩展告诉 web 服务器在将它们提供给最终用户的浏览器之前，先将它们通过 ColdFusion 应用程序服务器。一个带有。cfc 扩展是 ColdFusion 组件。这是 ColdFusion 在其他语言中的等价类。

##### 应用程序. cfc

根目录中的 Application.cfc 文件是一种特殊的组件，它在每个页面请求之前运行。它保存应用程序范围内的设置，并具有一组特殊的函数，这些函数在应用程序首次启动时、每个请求之前和之后、用户会话开始或结束时以及发生错误时运行。

在 ColdFusion Builder 项目的根文件夹中有一个 Application.cfc 文件。打开它，您会看到它设置了几个变量——应用程序名称和超时值——并且有一个函数，`onApplicationStart`:

```
<cfcomponent output="false">  

  <cfset this.Name="URLShortener">  
  <cfset this.ApplicationTimeout=CreateTimeSpan(0,0,5,0)>  

  <cffunction name="OnApplicationStart" access="public"  
      returntype="boolean" output="false"  
      hint="Fires when the application is first created">  
    <!--- delete and recreate the content cfc  
          stored in the application scope --->  
    <cfset StructDelete(application,"oContent")>  
    <cfset application.oContent=CreateObject("component","com.content")>  
     <cfreturn true>  
  </cffunction>  

</cfcomponent>
```

因为我们的 URL Shortener 应用程序中有多个文件，并且它们都需要使用 content.cfc 中的函数，所以我们将在应用程序启动时将该组件加载到应用程序范围中。这意味着在任何页面请求中，我们都可以通过在 content.cfc 中添加前缀`application.oContent`来引用其中的任何函数。

##### 第一，形式

家务已经做得够多了，让我们开始写代码吧。为了让我们的用户输入他们的网址来缩短，我们需要一个表单。打开 ColdFusion Builder 项目根目录中的 add.cfm，您将看到一个基本表单。

在表单的顶部是一些代码，当一个名为`form.action`的变量被定义时运行。本质上，我们的 content CFC 中的函数在循环内部运行，直到循环的退出条件——`shortlink`变量不是零长度——发生。我们一会儿将检查这里实际上发生了什么。在文件中向下跳转，您将看到一个标准的 HTML 模板，其中包含一点 ColdFusion 条件显示代码——检查变量是否存在，如果找到，则显示它——以及一个 HTML 表单，用于输入我们要缩短的 URL。相当简单的东西。

```
<!--- display the add form --->  
<form action="add.cfm" method="post">  
  <label for="link">URL to shorten</label>  
  <input type="text" name="link" id="link" size="100"><br>  
  <input type="submit" name="action" value="Submit">        
</form> 
```

##### 繁重的工作:生成短链接代码

为了生成我们的短 URL，我们将创建一个函数，它从可接受的字符池中返回一个预定义长度的随机字符串。我添加了 63 个可能的字符——大写字符 a-z，小写字符 A-Z，数字 0-9 和破折号(-)。由这些字符组成的六个字符的字符串产生 636 种可能的组合:即 62，523，502，209 个 URL，这应该足够让我们运行相当长一段时间了！

打开项目根目录下/com 目录中的 Content.cfc 来查看这个函数(它是列出的第一个函数)。您将看到函数`generateShowLink`带有一个参数`length`，这是必需的，但如果没有提供，则默认值为 6:

```
<cffunction name="generateShortLink" returntype="string">   

<cfargument name="length" type="Numeric" required="true" default="6">   
  <cfset var local=StructNew()>
```

这是将为短代码生成的字符数。如果您希望使用更多或更少的字符，您可以简单地在函数被调用时向其传递一个值`length`。在这段代码中，我们还设置了一个名为`local`的变量结构来存储局部变量。

首先我们建立一个所有允许字符的列表，然后定义我们的*基数*——即可能字符的总数:

```
<!--- create a list of all allowable characters for our short URL link --->   
  <cfset local.chars="A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,   
        T,U,V,W,X,Y,Z,a,b,c,d,e,f,g,h,i,j,k,l,m,n,   
        o,p,q,r,s,t,u,v,w,x,y,z,0,1,2,3,4,5,6,7,8,9,-">   
  <!--- our radix is the total number of possible characters --->   
  <cfset local.radix=listlen(local.chars)>

Then, after setting our return variable to an empty string, we're going to add random characters from that list in a loop:
```

```
<!--- initialise our return variable --->   
<cfset local.shortlink="">   
<!--- loop from 1 until the number of characters our URL should be,   
      adding a random character from our master list on each iteration  --->   
<cfloop from="1" to="#arguments.length#" index="i">   

  <!--- generate a random number in the range of 1   
        to the total number of possible characters we have defined --->   
  <cfset local.randnum=RandRange(1,local.radix)>   

  <!--- add the character from a random position in our list to our short link --->   
  <cfset local.shortlink=local.shortlink   
   & listGetAt(local.chars,local.randnum)>   

</cfloop>
```

最后，将新生成的短链接返回到调用代码:

```
<!--- return the generated random short link --->   
  <cfreturn local.shortlink>   

</cffunction> 
```

当我们将链接和短 URL 代码插入数据库时，我们需要确保短链接值在数据库中是唯一的。为了帮助解决这个问题，我们将数据库中的`shortlink`列设置为惟一的，因此如果我们试图插入一个重复的值，数据库将会抛出一个错误。我们要利用这一点。

content.cfc 中实际处理将数据插入数据库的函数称为`insertContent`。它接受一个参数，`link`——我们希望缩短的 URL 在顶部定义，然后初始化一个本地结构，该结构将保存我们需要在此函数中本地使用的变量:

```
<cffunction name="insertContent" returntype="string">   
<cfargument name="link" type="String" required="true">   
<cfset var result=StructNew()>
```

接下来，在一个捕捉错误的`cftry`块中，我们将尝试插入链接和一个由我们的`generateShortlink`函数生成的短链接代码。如果`shortlink`值是惟一的，那么插入将会成功，我们的`insertContent`函数将会返回新值以显示给最终用户。

```
<!--- begin our error-catching block --->   
<cftry>   

  <!--- try to insert the new link into the database --->   
  <cfquery datasource="#variables.dsn#" name="result.qry" result="result.stats">   
    INSERT INTO   
      content(   
        link,   
        shortlink   
        )   
    VALUES(   
      <cfqueryparam cfsqltype="cf_sql_varchar" value="#arguments.link#">,   
      <cfqueryparam cfsqltype="cf_sql_varchar" value="#generateShortLink()#">   
        )   
  </cfquery>   
  <!--- try to return the new shortlink value,   
        referencing the last returned identifier --->   
  <cfquery datasource="#variables.dsn#" name="result.inserted">   
    SELECT   
      shortlink   
    FROM   
      content   
    WHERE   
      id = <cfqueryparam cfsqltype="cf_sql_integer" value="#result.stats.IDENTITYCOL#">   
  </cfquery>
```

如果生成的短代码恰好是一个已经被使用过的代码呢？该函数将命中捕捉错误的 cfcatch 块，结果将被设置为空字符串:

```
 <cfcatch>   
      <!--- insert was not successful - the shortlink generated   
            was not unique, so set the return variable to an   
            empty string --->   
      <cfset result.inserted.shortlink="">   
  </cfcatch>    

</cftry>  
```

该函数做的最后一件事是返回新插入的短链接或空字符串:

```
<!--- return either the newly created shortlink,   
            or an empty string if an error occurred --->   
<cfreturn result.inserted.shortlink>    
</cffunction>
```

在我们的 add.cfm 组件中，我们在一个条件循环中调用`insertContent`函数:

```
<cfloop condition="len(shortlink) EQ 0">   
<cfset shortlink=application.oContent.insertContent(link)>   
</cfloop>
```

这个循环将继续运行，生成新值并尝试将它们插入数据库，直到成功且返回值不是空字符串。

add.cfm 模板然后设置`message`变量来保存新生成的短 URL:

```
<cfset message="Your new short URL is http://#cgi.server_name#:#cgi.server_port#/URLS/?#shortlink#">
```

`message`变量就在表单上方显示给最终用户:

```
<!--- if there is a variable defined called "message", display it --->   
<cfif isDefined("message")>   
  <cfoutput><p>#message#</p></cfoutput>   
</cfif>
```

##### 最后一步——处理缩短的 URL

拼图的最后一块是代码，它实际上接受我们缩短的 URL 并将最终用户重定向到真正的链接。这段代码在 index.cfm 中。

首先，我们使用内置的`cgi`变量读取当前请求的查询字符串的内容，并使用`Replace`函数删除查询字符串开头的问号。

```
<!--- remove the question mark from the beginning of the URL --->   
<cfset shortlink=Replace(cgi.query_string,'?','')>
```

因为我们使用内置的开发服务器，这是非常基本的，所以我们很难生成更简洁的短 URL——也就是说，其中没有问号的 URL。如果您正在构建一个 URL 缩短应用程序，该应用程序将部署在运行于 Apache 或 IIS 服务器上的 ColdFusion 上，您将能够从等式中去掉问号。

接下来，我们使用 content.cfc 中的`getLink`函数从数据库中检索完整的链接记录:

```
<!--- retrieve the full URL --->   
<cfset link=application.oContent.getLink(shortlink)>
```

跳回到 content.cfc，这个`getLink`函数对一个必需的参数`shortlink`执行简单的数据库查找，并返回原始链接值:

```
<cffunction name="getLink" returntype="string">   
  <cfargument name="shortlink" type="string" required="true">   
  <cfset var result="">   
  <cfquery datasource="#variables.dsn#" name="result">   
    SELECT   
      Link   
    FROM   
      content   
    WHERE   
      shortlink = <cfqueryparam cfsqltype="cf_sql_varchar" value="#arguments.shortlink#">   
  </cfquery>   
  <cfreturn result.link>   
</cffunction>
```

最后，我们获取从数据库返回的链接值。如果有一个有效的 URL，我们使用一个`cflocation`标签将用户重定向到这个链接。但是，如果没有返回任何内容 link 的值的长度为零——那么我们将显示一条错误消息。

```
<!--- if a URL is returned, relocate to it; otherwise show an error message --->   
<cfif len(link) GT 0>   
  <cflocation url="#link#">   
<cfelse>   
  <p>Link not found.</p>   
</cfif>
```

##### 丰富

这个例子非常简单，如果你愿意，有很多方法可以改进它。首先，没有输入变量的验证——如果输入的 URL 无效，URL 添加表单显示一条消息就好了。我肯定你还能想到更多！

请继续关注本教程的后续内容，我们将扩展我们的网址缩写器，增加一个评级部件。现在，[在我们的 ColdFusion 测验](https://www.sitepoint.com/quiz/adobe/get-shorty-coldfusion/)中试试你的技能，该测验由 Adobe 自豪地赞助，以检查你已经吸收了多少本教程。

## 分享这篇文章