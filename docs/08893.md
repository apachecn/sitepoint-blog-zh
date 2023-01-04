# Google App Engine (GAE) Java API 第 2 部分:设置和介绍

> 原文：<https://www.sitepoint.com/understanding-google-app-engine-gae-java-api-part-2-setup-and-introduction/>

在这个系列的第一部分中，我们介绍了 GAE 为开发者提供的基本环境。您一定很兴奋能够编写自己的第一个应用程序，并看到它的现场直播！为 GAE 开发设置 IDE 是一项相当简单的任务:只需在您选择的 Eclipse 版本中安装 GAE 插件，就可以在 GAE 上开始开发了。

但是在我们真正进入 GAE 能提供的 API 和服务之前，让我们了解一下影响你的应用在运行时如何工作的应用结构和配置。这还将带我们了解环境中一些有趣的方面，并引导我们更好地设计和规划应用程序。

## 部署描述符:web.xml

GAE 上的 web 应用程序的部署描述符与任何其他 web 应用程序非常相似，您可以以相同的方式配置 Servlet 和 JSP URL 映射，或者以与 web 应用程序相同的方式保护 URL 和指定角色。过滤器和错误处理程序也可以以同样的方式使用。需要注意一些小的区别:EJB/JNDI 之类的不被支持，这很明显，因为我们没有 EJB 容器。启动时加载不是在应用程序启动时加载 servlet，而是在 web 服务器收到第一个请求时加载。可以配置一些常见的配置，如配置 ServletContextListener 或启动时加载参数，但这些配置放在 appengine-web.xml 中。

## 应用程序配置:appengine-web.xml

appengine-web.xml 文件是 GAE web 应用程序的强制性要求，它至少包含应用程序 id 和版本。在部署应用程序时，版本号用于决定如果找到匹配，是部署到现有版本，还是如果没有找到匹配，则部署到新版本。这对于测试应用程序的多个实例非常有帮助，同时不需要接触实际的实例。但是请注意，数据存储是在应用程序的不同版本之间共享的。

```
<?xml version="1.0" encoding="utf-8"?>
<appengine-web-app >
 <application>my-app</application>
 <version>2</version>
</appengine-web-app>
```

在上面的 XML 中，部署了 my-app 应用程序的版本 2。

## 提供静态内容

静态内容可以用<static-files>标签来标记，这样所有的静态内容都由一个单独的 web 服务器来提供，正如你可能知道的，这在 web 应用程序中是一个好的实践。静态内容在应用程序的生命周期中变化不大，静态服务器提供的服务让容器/应用程序服务器专注于它最擅长的动态内容。这包括首先考虑的表达式，并且即使你不指定一个默认的一旦被应用，随后应用的排除表达式。</static-files>

以下示例将所有 jpg 文件标记为静态，但 classes 目录中的文件除外。可以为带有标签的资源文件定义类似的表达式。include 标记中的 expiration 参数设置浏览器缓存的过期时间，例如在下面的示例中设置为 36 小时。

```
<static-files>
       <include path="/**.jpg" expiration="1d 12h />
       <exclude path="/classes/**.jpg" />
</static-files>
```

## 系统和环境属性

可以使用以下标记设置系统和环境级别的属性:

```
    <system-properties>
           <property name="my-app.user.timeout" value="300" />
    </system-properties>
    <env-variables>
       <env-var name="ENV_VAR_NAME" value="ENV_VAR_VALUE" />
    </env-variables>
```

## 配置入站服务

某些入站服务，如 mail 或 XMPP，默认情况下是禁用的。如果需要启用它们，需要专门将它们添加到 appengine-web.xml 中，如下例所示。

```
<inbound-services>
       <service>channel_presence</service>
</inbound-services>
```

<colgroup><col width="*"> <col width="*"></colgroup>
| 服务 | 描述 |
| 邮件 | 启用接收电子邮件 |
| 渠道 _ 存在 | 允许客户端使用通道并启用通知 |
| 变暖 | 用于在启动时预热某些应用程序组件 |
| xmpp_presencexmpp _ 消息xmpp_subscribe | XMPP 相关服务 |

## 会话机制

GAE 使用 servlet 会话接口提供了一种会话机制，但默认情况下是禁用的，要启用它，您需要向 appengine-web.xml 添加以下内容:

```
<sessions-enabled>true</sessions-enabled>
```

会话的数据存储在具有 type _ah_SESSION 实体的数据存储中，并且也存储在 MEMCACHE 中，以便更快地访问。您可以通过将写入数据存储的会话数据标记为异步来减少延迟。会话数据写入数据存储由队列完成，如果未指定，则为“默认”队列，但可以配置为其他名称。

```
<async-session-persistence enabled="true" queue-name="sessionQueue"/>
```

## 索引配置

索引用于加速数据的检索。在 GAE，索引可以由开发者/用户定义，也可以由开发服务器自动生成。

<colgroup><col width="240"> <col width="384"></colgroup>
| 文件名 | 细节 |
| WEB-INF/datastore-indexes.xml | 用户/开发者定义的索引 |
| we b-INF/app engine-generated/datastore-indexes-auto . XML | 对数据进行查询时由开发服务器自动生成的索引 |

让我们看看典型的数据存储结构——indexes . XML 及其控制的两个关键方面。

```
<?xml version="1.0" encoding="utf-8"?>
<datastore-indexes
 autoGenerate="true">
   <datastore-index kind="Employee" ancestor="false">
       <property name="employeeNumber" direction="asc" />
   </datastore-index>
</datastore-indexes>
```

先看子元素，<datastore-index>。此标签定义了您将为其定义索引的实体种类。如果索引支持按父实体组筛选实体，则祖先属性应为 true。属性标签定义了属性的名称，即索引和方向，可以是“asc”或“dsc”。</datastore-index>

在文件的父标签中定义的第二个方面，即在<datastore-indexes>中控制如何处理自动生成的索引。</datastore-indexes>

<colgroup><col width="154"> <col width="470"></colgroup>
| autoGenerate="true " | –如果用户定义的索引文件不存在，则默认为 true。–当开发服务器命中任一索引文件中未定义索引的查询时更新，并将索引添加到 datastore-indexes-auto.xml–当您将应用程序上传到 GAE 时，两个索引文件都用于在 GAE 上建立索引 |
| autoGenerate="false " | –忽略 datastore-indexes-auto.xml 中的索引–当应用程序遇到 datastore-indexes.xml 中没有定义索引的查询时，会引发异常 |

## 计划任务:cron.xml

### 定义和调度 crons

Cron 对于类 UNIX 操作系统用户来说是一个非常熟悉的词，它是一个强大的工具，可以定期或一次性地完成一些任务，但它会在指定的时间/间隔由系统自己触发。GAE 克朗斯易于使用，并能很好地插入你的网络应用程序。作业是在 servlet 中定义的，您可以在部署描述符中为它配置 URL。作业和计划的 URL 只是在 cron.xml 中配置 cron 的两个强制性要求。如果您不定义 target，它将使用默认的应用程序实例，但您可以选择针对要运行的实例的特定版本启动 cron。Cron 文件最多可以有 20 个 cron，XML 文件驻留在应用程序的 WEB-INF 目录中。

时间表格式几乎是英文的，如“每 6 小时”或“1 月、4 月、6 月、10 月的第一个星期二 05:00”

```
<?xml version="1.0" encoding="UTF-8"?>
<cronentries>
 <cron>
   <url>/DailyReport</url>
   <description>Optional Desc goes here</description>
   <schedule>every day 11:30</schedule>
   <timezone>America/New_York</timezone>
   <target>version-2</target>
 </cron>
</cronentries>
```

### 访问和保护 Cron 作业

只要 cron 的“url”中的条目可以作为 web 应用程序的 URL 访问，cron 作业就应该运行良好。您需要注意的下一件事是，通过在部署描述符中添加可以访问 cron 的 URL 和相应的管理员角色，不允许除 system/admin 之外的任何用户访问 cron。

到目前为止，我们已经了解了几乎所有的 GAE 配置和环境。我们没有讨论后端和任务队列配置，因为它们在那些特殊主题的上下文中会得到更恰当的解释。在地面和天空演示之后，我们现在准备潜水！因此，请继续关注下一部分，并设置您的 IDE 和大脑以便动手操作！

## 分享这篇文章