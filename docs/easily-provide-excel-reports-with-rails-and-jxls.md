# 使用 Rails 和 jXLS 轻松提供 Excel 报表

> 原文：<https://www.sitepoint.com/easily-provide-excel-reports-with-rails-and-jxls/>

![](img/8745f9bd0123dbb7a240926b029c474a.png)

在任何应用程序中，报告都是最重要的需求之一，如果我们正在处理一个业务应用程序，这些需求甚至会变得更加重要。然而，提供屏幕报告或漂亮的 HTML 或 PDF 报告并不总是让用户满意。有时，用户需要 Excel 报表，以便他们可以进一步处理数据，从而获得他们想要的结果。

幸运的是，Ruby 提供了许多与 Excel 相关的库，但是它们中的大多数使用编程方式来创建 Excel，这对于更复杂的报表来说会很快变得复杂和令人厌烦。但是我们有一个解决方案: [jXLS](http://jxls.sourceforge.net) 。这是一个流行的 Java 库，用于从模板 Excel 文件生成 Excel 文件。多亏了 JRuby，我们可以在 Rails 中使用这个优秀的库。事不宜迟，让我们开始吧。

## 创建基本的 Rails 应用程序

我们将创建一个带有发票模型和 CRUD 操作的基本 Rails 应用程序。该应用程序将使用 JRuby、Rails 4.2 和 SQLite 来保持简单。首先用 [RVM](https://rvm.io) 或者 [rbenv](http://rbenv.org) 安装 JRuby。

切换到 JRuby 和`gem install rails`获得最新的 Rails gem。现在创建一个新的 Rails 应用程序，如下所示:

```
rails new jxls_rails -T 
```

生成应用程序后，创建一个发票支架，它有一个模型和必要的 CRUD 资源操作:

```
cd jxls_rails
rails g scaffold Invoice invoice_number:string invoice_date:string customer:string total_value:decimal 
```

现在迁移数据库:

```
rake db:migrate 
```

让我们来看看它是如何工作的:

```
rails s 
```

将你最喜欢的浏览器指向[http://localhost:3000/invoices](http://localhost:3000/invoices)，确保一切正常运行。创建一些记录，以便我们以后使用。

## 创建 Excel 报表

首先，让我们创建一个 Excel 电子表格作为报告模板。自从上了 Ubuntu，我就在用 LibreOffice。在 **app/reports** 目录下保存为 **invoices.xls** 。添加字段和注释，如下面的屏幕截图所示。

![sample-excel](img/842f3b67cf05fcec186884fbf28ac798.png)

我们通过添加标题、数据占位符和 jXLS 相关元数据的注释来定义报告模板。如您所见，我们可以在模板中随意设置单元格的格式，因此不需要以编程方式进行管理。

我们基本上已经为数据设置了占位符，它们看起来像`${invoice.invoice_number}`，将被实际数据所取代。第一个注释`jx:area(lastCell="D2")`，通过定义报告范围中的最后一个单元格来设置报告的工作区域。另一个注释`jx:each(items="jdbc.query(query)" var="invoice" lastCell="D2")`，指定遍历所有记录并通过替换占位符来填充行的迭代器标记。这里的`items`是报表的行集合。我们将报告设计为直接执行提供的 SQL 查询，并通过使用`jdbc.query(query)`作为`items`来填充报告。我们也可以传入一组对象。

我们现在已经创建了一个简单的 Excel 电子表格。还有一些高级选项，可以在 [jXLS 文档](http://jxls.sourceforge.net/getting_started.html)中找到。

## 将 jXLS 集成到 Rails 中

首先，从 [jXLS SourceForge](https://sourceforge.net/projects/jxls/files/jxls-2/2.2.9/) 页面下载 jXLS 文件。我们使用的是 2.2.9 版本，所以下载 **jxls-2.2.9.zip** 并解压文件。在我们的应用程序中，将所有文件从 **dist** 文件夹复制到 **lib/jxls** 中。

还有，从[这个链接](https://sourceforge.net/projects/jxls/files/jxls-2/2.2.6/jxls-dependencies.zip/download)下载 jXLS 的所有依赖项。在我们的应用程序中，将 **lib** 文件夹中的所有文件解压并复制到 **lib/jxls** 中。

我们最终的目录结构如下所示:

```
jxls_rails
  - app
  ...
  - lib
    - jxls
      - jxls-2.2.9.jar
      ... 
```

现在我们已经准备好了所有需要的文件。让我们用下面的代码在 **lib** 目录下创建一个名为 **jxls.rb** 的文件:

```
Dir.entries("#{Rails.root}/lib/jxls").each do |lib|
  require "jxls/#{lib}" if lib =~ /\.jar$/
end

require 'java'

java_import Java::org.jxls.common.Context
java_import Java::org.jxls.util.JxlsHelper
java_import Java::org.jxls.jdbc.JdbcHelper
java_import Java::java.io.BufferedInputStream
java_import Java::java.io.FileInputStream
java_import Java::java.io.ByteArrayOutputStream
java_import Java::org.jxls.util.TransformerFactory
java_import Java::org.jxls.transform.poi.PoiTransformer

class Jxls
  DIR = "#{Rails.root}/app/reports"

  def initialize(report, query)
    @filename = report
    @conn = ActiveRecord::Base.connection.jdbc_connection
    @query = query
  end

  def to_xls
    beans = {}
    report_source = BufferedInputStream.new(FileInputStream.new("#{DIR}/#{@filename}.xls"))
    raise ArgumentError, "#@filename does not exist." unless File.exist?("#{DIR}/#{@filename}.xls")
    bos = ByteArrayOutputStream.new
    context = Context.new
    jdbc_helper = JdbcHelper.new(@conn)
    context.put_var("jdbc", jdbc_helper)
    context.put_var("query", @query)
    JxlsHelper.getInstance().processTemplate(report_source, bos, context)
    bos.close
    bytes = bos.toByteArray
    return String.from_java_bytes(bytes)
  end
end 
```

好了，让我们浏览一下这段代码，看看它到底做了什么。首先，我们需要将所有 jXLS 文件放入我们的类中，就像这样:

```
Dir.entries("#{Rails.root}/lib/jxls").each do |lib|
  require "jxls/#{lib}" if lib =~ /\.jar$/
end 
```

为 jXLS 添加 Java 导入声明:

```
require 'java'

java_import Java::org.jxls.common.Context
java_import Java::org.jxls.util.JxlsHelper
java_import Java::org.jxls.jdbc.JdbcHelper
java_import Java::java.io.BufferedInputStream
java_import Java::java.io.FileInputStream
java_import Java::java.io.ByteArrayOutputStream
java_import Java::org.jxls.util.TransformerFactory
java_import Java::org.jxls.transform.poi.PoiTransformer 
```

定义所有报告的存储位置:

```
DIR = "#{Rails.root}/app/reports" 
```

在类构造函数中添加初始化代码:

```
def initialize(report, query)
  @filename = report
  @query = query
  @conn = ActiveRecord::Base.connection.jdbc_connection
end 
```

作为第一步，我们初始化了所有需要的变量:

*   报告的文件名—`report`
*   报告的 SQL 查询-`query`

`@conn`是 ActiveRecord 连接池中的一个 JDBC 连接，因为 jXLS 将需要一个 JDBC 连接来执行报告中指定的查询。如果我们打算传递一个对象集合，那么这个连接可能是不需要的。

最后，我们添加了一个方法来填充报告并将其导出为 Excel 格式:

```
def to_xls
  report_source = BufferedInputStream.new(FileInputStream.new("#{DIR}/#{@filename}.xls"))
  raise ArgumentError, "#@filename does not exist." unless File.exist?("#{DIR}/#{@filename}.xls")
  bos = ByteArrayOutputStream.new
  context = Context.new
  jdbc_helper = JdbcHelper.new(@conn)
  context.put_var("jdbc", jdbc_helper)
  context.put_var("query", @query)
  JxlsHelper.getInstance().processTemplate(report_source, bos, context)
  bos.close
  bytes = bos.toByteArray
  return String.from_java_bytes(bytes)
end 
```

之前创建的报表( **invoices.xls** )文件被设置为`report_source`。接下来，我们将`jdbc_helper`和`@query`添加到要传递给报表的`context`对象中。最后，我们调用`JxlsHelper.getInstance().processTemplate`将报告填充并导出到 Excel bytestream 中，使用`String.from_java_bytes`返回该报告。

我们现在有一个 Excel bytestream 格式的报告，但是我们还没有办法把它发送给用户。所以，在**application _ controller . Rb**中添加一个小的 helper 方法如下:

```
def respond_to_report(name, query, filename, download = false)
  @report = Jxls.new(name, query)
  disposition = (download.nil? || download == false) ? 'inline' : 'attachment'
  send_data @report.to_pdf, :filename => filename, :type => :xls, :disposition => disposition
end 
```

这个助手方法简化了报告调用，并将响应发送回用户。还增加了提供文件名的选项，以及在浏览器中打开文件或将其作为附件下载的`disposition`选项。

我们需要做的另一件事是添加一个初始化器，用于在 Rails 中加载我们的`JXLS`类。用下面的代码创建一个名为**config/initializer/jxls . Rb**的文件

```
require 'jxls' 
```

现在，我们将向调用报告的`InvoicesController`添加一个动作。将以下代码添加到**app/controllers/invoices _ controller . Rb**:

```
def report
  respond_to_report('invoices', 'select * from invoices', 'invoices.xls')
end 
```

因为我们没有要传递的参数，并且想要在浏览器中打开报告，所以我们在方法调用中省略了`download`和`report_params`。

更新 **routes.rb** 以添加新动作:

```
resources :invoices do
  get :report, on: :collection
end 
```

在 **invoices/index.html.erb** 视图中添加报告链接:

```
...
<h1>Listing Invoices</h1>

<%= link_to 'Download as Excel', report_invoices_path %>
... 
```

启动服务器，进入[http://localhost:3000/invoices](http://localhost:3000/invoices)。点击“下载为 Excel”。现在，您应该会看到一个 Excel 文件，其中列出了所有发票。

![final-excel](img/b8b470b6effd2be5e74f97e2c752cde4.png)

## 包扎

今天，我们快速了解了如何在 Rails 应用程序中使用 jXLS 来快速生成 Excel 报表。jXLS 为复杂的报告提供了更高级的选项，如公式、图表、宏等。但那是以后的事了。

欢迎您的评论和见解。

## 分享这篇文章