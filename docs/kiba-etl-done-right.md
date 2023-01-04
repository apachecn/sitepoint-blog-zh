# Kiba: ETL 做得对吗

> 原文：<https://www.sitepoint.com/kiba-etl-done-right/>

![Screenshot 2015-06-25 12.57.05](img/18cadd3854e1d75accaa8f84aec616dd.png)

我是一名“顾问”或“承包商”，这是人们对我的称呼中以“con”开头的两个词。本质上，我的客户有一种渴望，他们想用技术来挠痒痒，他们雇佣我的公司来做这项工作。

> 我并不热衷于“顾问”这个术语，但是如果你没听过的话，这是一个很棒的笑话。

作为一名数码枪手，我经常参与将应用程序和系统从一个平台转移到另一个平台。从 ASP 到 Ruby 或者从 SQL Server 到 PostgreSQL。在许多情况下，存在于“旧”系统中的数据需要移动/升级/转换到“新”系统。规范的术语是“数据迁移”，它让那些由于项目的复杂性而项目逾期数月或数年的人感到恐惧。这是我最后一次工作范围(SOW)要求进行数据迁移:

![kiba1](img/355d6c020945e0d532be56c6f50ca8db.png)

即使看似简单的迁移也可能很糟糕。这些就是我在今天的帖子中要看的那种。简单的迁移，从一个源提取数据，做一些转换，然后将数据存储到新的目的地。我需要将用户信息从一个系统迁移到我们正在构建的新系统。迁移的想法让我牙齿打颤，直到我找到基巴。

## 基巴来救援了

[Kiba](http://www.kiba-etl.org/) 是“一个轻量级的 Ruby ETL 框架”。ETL，对于不知道的人来说，代表 **E** xtract，**T**transform， **L** oad。ETL 是“数据迁移”的另一种说法，但它更接近前线。ETL 通常意味着有一个或多个已知的源，以及已知的目的地。当您准备好谈论 ETL 时，您正在谈论如何从源中提取数据，更改(转换)它以满足需求，然后将它写入(加载)到新的目的地。就是这样。三个简单的步骤可以吃掉你的项目时间线就像我吃煎饼一样(我喜欢煎饼。)

![kiba2](img/ae7c971f50ed11b04472c518e72c07ef.png)

Kiba 旨在解决这个问题，我不得不说，我非常兴奋地发现它。在我多年从事这项工作的过程中，我观察了各种 ETL 解决方案，对此我持怀疑态度。我欣喜若狂地说，Kiba 简单灵活。

Kiba Github 仓库对 Kiba 如何工作有一个很好的概述，比我在这里要详细得多。读完这篇文章后，你可以在那里获得更多的 Kiba 信息。

Kiba 是由 activewarehouse-etl gem 的前维护者 Thibaut Barrere 创建的。

## 作为流水线的数据处理

在一篇出色的博客文章中，Thibaut 讲述了如何编写数据处理代码。要点是，它应该像一条*管道*，源将数据输入一端，数据经过各种转换处理，然后从管道的另一端进入目的地。

![kibapipeline](img/28458cfa1846fb138c52193ad701480f.png)

上面的图片应该能说明问题。source (MySQL)提供第一个管道。您可以将尽可能多的转换管道连接在一起，组成您的管道。上面，中间的管道可以从上面添加东西，或者从下面过滤东西。显然，这些管道可以添加/删除/更改数据，但是它们也可以是有条件的。您的转换可以对传入的数据做出反应，在您需要的时候做您需要的事情。

此外，Kiba 允许创建可重用的转换，这意味着您可以将管道带到任何地方。这些转换可以独立测试，为您的 ETL 带来一个完全成熟的开发周期。

除了源、转换和目的地，Kiba 还提供了其他一些好东西。一个 ETL 项目可以包括前处理器和后处理器。在访问源之前运行一个前处理器，在所有行都写入目标之后运行一个后处理器。

以下是从 Kiba 文档转换而来的示例:

```
# declare a ruby method here, for quick reusable logic
def parse_french_date(date)
  Date.strptime(date, '%d/%m/%Y')
end

# or better, include a ruby file which loads reusable assets
# eg: commonly used sources / destinations / transforms, under unit-test
require_relative 'common'

# declare a pre-processor: a block called before the first row is read
pre_process do
  # do something
end

# declare a source where to take data from (you implement it - see notes below)
source MyCsvSource, 'input.csv'

# declare a row transform to process a given field
transform do |row|
  row[:birth_date] = parse_french_date(row[:birth_date])
  # return to keep in the pipeline
  row
end

# declare another row transform, dismissing rows conditionally by returning nil
transform do |row|
  row[:birth_date].year < 2000 ? row : nil
end

# declare a row transform as a class, which can be tested properly
transform ComplianceCheckTransform, eula: 2015

# before declaring a definition, maybe you'll want to retrieve credentials
config = YAML.load(IO.read('config.yml'))

# declare a destination - like source, you implement it (see below)
destination MyDatabaseDestination, config['my_database']

# declare a post-processor: a block called after all rows are successfully processed
post_process do
  # do something
end
```

这个 ETL 项目非常容易理解，应该可以让您体验一下 Kiba 是如何操作的。

## 完成提取设置，时间来转换这个职位和加载演示

此时，在我完成 ETL 项目的过程中，您对 Kiba 的理解应该会对您有所帮助。正如我前面提到的，我需要将用户帐户从一个系统迁移到另一个系统。这是细目分类:

| Kiba 角色 | 完成者 |
| 来源 | 关系型数据库 |
| 改变 | 忽略密码错误的帐户 |
| 改变 | 获取电子邮件、密码和盐 |
| 目的地 | 一种数据库系统 |

这很简单。ETL 将从 MySQL 表中提取用户，过滤他们，然后将他们写入 PostgreSQL。

### 来源

Kiba 源是一个类，它:

*   有一个构造函数，可以设置特定于源的任何需求。对于 CSV 源文件，它可能是文件名。
*   实现`each`，向提供的块产生行。

这种设计使得源很容易隔离，并且非常容易测试。事实上，它们非常简单，你可能不需要太多的测试。

我的来源是一个 MySQL 数据库，看起来像:

*lib/user_source.rb*

```
require 'mysql2'
require 'uri'

class UserSource
  # connect_url should look like;
  # mysql://user:password@localhost/dbname 
  def initialize(connect_url)
    @mysql = Mysql2::Client.new(connect_hash(connect_url))
  end

  def each
    results = @mysql.query('select * from accounts', as: :hash, symbolize_keys: true)
    results.each do |row|
      yield(row)
    end
  end

  private

  def connect_hash(url)
    u = URI.parse(url)
    {
      host: u.host,
      username: u.user,
      password: u.password,
      port: u.port,
      database: u.path[1..-1]
    }
  end
end
```

我用一个 MySQL URI 初始化源代码。`each`方法通过一个简单的查询来获取账户，然后将每一行生成到块中。说真的，这有多容易？最糟糕的部分是解析 URI。

以上来源很简单，是为了让它更(我希望)可教。通过将查询传递给构造函数，您可以使它更加通用(想想`MySQLSource`)。此外，对于较大的负载，您可能希望使用 MySQL 流。关键是，这很容易做到，由你决定。

### 目的地

Kiba 目的地是这样一个类:

*   具有构造函数的原因与源具有构造函数的原因相同。目的地的任何初始化需求都可以在这里传递。
*   实现一个`write(row)`方法将行写入目标。
*   实现一个`close`方法来清理自身。

这个项目中的目标是一个 PostgreSQL 数据库:

*lib/user_destination.rb*

```
require 'pg'

class UserDestination
  # connect_url should look like;
  #  mysql://user:pass@localhost/dbname
  def initialize(connect_url)
    @conn = PG.connect(connect_url)
    @conn.prepare('insert_user_stmt', 'insert into users (email, password_digest, created_at, updated_at) values ($1, $2, $3, $4)')
  end

  def write(row)
    time = Time.now
    @conn.exec_prepared('insert_user_stmt', 
                        [ row[:email], row[:password], row[:date_created], time ])
  rescue PG::Error => ex
    puts "ERROR for #{row[:email]}"
    puts ex.message
    # Maybe, write to db table or file
  end

  def close
    @conn.close 
    @conn = nil
  end
end
```

如您所见，在初始化目的地时，我创建了一个准备好的语句。这是一个 PostgreSQL 优化，展示了如何使用构造函数进行特定于目的地的设置。

`each`函数通过执行准备好的语句简单地写入行。`row`是一个散列，带有我们想要的属性的键。目标表也有时间戳列(`updated_at`、`created_at`)，所以我解释了这些。

在`write`中有一个`rescue`子句来捕获错误并跟踪哪些记录死亡是一个好主意。此外，如果您没有捕获错误，迁移将在第一个错误时停止，这通常是不可取的。

正如我提到的`UserSource`，这个类可以在许多方面变得更加通用。

现在，是时候做我们的管子了。

### 管道

因为这是一个非常简单(但是非常真实)的 ETL 例子。`transform`只是从源获取属性，并将它们映射到目的地的属性，然后返回新行。**yawwwns**

```
transform do |row|
  newrow = {}
  newrow[:email] = row[:Email]
  newrow[:password] = row[:Password]
  newrow
end
```

Kiba 提供了许多实现转换的方法，您可以将它们链接起来。链式转换将按照它们在脚本中的顺序执行(我们很快就会看到)。例如，上面的转换可以改为:

```
transform do |row|
  row[:crypt] == "bcrypt" ? row : nil
end

transform do |row|
  newrow = {}
  newrow[:email] = row[:Email]
  newrow[:password] = row[:Password]
  newrow
end
```

在这种情况下，我们根据属性从管道中删除源行。干净利落。

也可以创建一个执行转换的 Ruby 类。像这样:

```
class FilterMD5
  def initialize(value_to_filter) 
    @value = value_to_filter
  end

  def process(row)
    row[:crypt] == value_to_filter ? row : nil
  end
end
```

然后，转换变为:

```
transform FilterMD5, "md5"

transform do |row|
  newrow = {}
  newrow[:email] = row[:Email]
  newrow[:password] = row[:Password]

  newrow
end
```

同样，`FilterMD5`可以变得更通用，也许接受一个允许不同过滤器的块。就当是家庭作业吧。

将转换放入类中的能力使得为转换编写规范/测试变得很简单。Kiba 支持最佳实践。*举起击掌的手*

### 前处理器和后处理器

我想介绍的 Kiba 的最后一部分是前处理器和后处理器。正如您所想象的，这些项分别在从源中检索任何记录之前和所有记录被写入目标之后运行。任何整体设置或清理都由这些处理器处理。在示例中，我用它来传达进度:

```
start_time = Time.now
pre_process do
  puts "*** Start ACCOUNT MIGRATION #{start_time}***"
end

... source, transforms, destination ...

post_process do
  end_time = Time.now
  duration_in_minutes = (end_time - start_time)/60
  puts "*** End ACCOUNT MIGRATION #{end_time}***"
  puts "*** Duration (min): #{duration_in_minutes.round(2)}"
end
```

那里。现在我有了一个很好的 UX 经验给脚本运行者，也就是我。干得好，我。

显然，我的例子是琐碎的，但是你可以想象真实的工作进入这些块的用例。对吗？

### 《光猪六壮士》

以下是我的 Kiba 脚本:

*migrate_users.rb*

```
#!/usr/bin/env ruby

$:.unshift "#{File.dirname(__FILE__)}/lib"

require 'user_source'
require 'user_destination'
require 'filter_md5'

mysql_url = ENV["MYSQL_URL"]

source UserSource, mysql_url

start_time = Time.now
pre_process do
  puts "*** START ACCOUNT MIGRATION #{start_time}***"
end

transform FilterMD5

transform do |row|
  puts "OK"
  newrow = {}
  newrow[:email] = row[:Email]
  newrow[:password] = row[:Password]
  newrow[:salt] = row[:Salt]
  newrow[:old_id] = row[:AccountId]

  newrow
end

post_process do
  end_time = Time.now
  duration_in_minutes = (end_time - start_time)/60
  puts "*** End ACCOUNT MIGRATION #{end_time}***"
  puts "*** Duration (min): #{duration_in_minutes.round(2)}"
end

destination UserDestination, ENV["PG_URL"]
```

源文件、目标文件和转换文件位于我的项目中的一个 **lib** 目录中。

### 运行它

运行 Kiba 脚本包括调用`kiba`可执行文件并传递您的脚本名:

```
kiba migrate_users.rb
```

开始了！

```
*** START ACCOUNT MIGRATION 2015-06-25 12:53:10 -0400***
....SOME ERRORS...
*** End ACCOUNT MIGRATION 2015-06-25 12:54:11 -0400***
*** 11433 records moved in 1.02 minutes
```

## ETL DSL 的 LOL

Kiba 让我非常喜欢用 Ruby 做 ETL。在我看来，这是一个设计非常好、功能强大的 DSL，填补了 Ruby 生态系统中的一个空白。我鼓励你去 Kiba 网站看看，在图书馆里多读些东西。文档很好，还有一些关于 ETL 和 Kiba 的博客文章。如果你最终使用了它，让蒂波特知道。我做了，他把我作为网站上的推荐人。:)

*走进厨房，做煎饼*

## 分享这篇文章