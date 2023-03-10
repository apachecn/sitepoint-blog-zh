# 用适配器设计模式改进您的 Ruby

> 原文：<https://www.sitepoint.com/using-and-testing-the-adapter-design-pattern/>

![](img/222940e000d33cff9a953d0d52f77850.png)

想象一下，我们有一些代码，我们想用各种方式完成事情。一种方法是使用条件分支:

```
class Animal
  def speak(kind)
    puts case kind
    when :dog then "woof!"
    when :cat then "meow!"
    when :owl then "hoo!"
    end
  end
end

Animal.new.speak(:dog) 
```

这是可行的，但是如果一个开发者想要增加一种新的方式呢？使用条件分支，整个方法将需要被覆盖。相反，我们可以将实现分成模块:

```
class Animal
  module Adapter
    module Dog
      def self.speak
        puts "woof!"
      end
    end

    module Cat
      def self.speak
        puts "meow!"
      end
    end
  end

  def speak
    self.adapter.speak
  end

  def adapter
    return @adapter if @adapter
    self.adapter = :dog
    @adapter
  end

  def adapter=(adapter)
    @adapter = Animal::Adapter.const_get(adapter.to_s.capitalize)
  end
end

animal = Animal.new
animal.speak
animal.adapter = :cat
aanimal.speak 
```

这段代码太多了！但是，如果我们想添加另一个模块，这并不太糟糕，而且更加灵活:

```
class Animal
  module Adapter
    module Owl
      def self.speak
        puts "hoo!"
      end
    end
  end
end

animal.adapter = :owl
animal.speak 
```

这个新模块甚至可以放在一个单独的 gem 中——并且有自己的依赖项！这种组织方式被称为**适配器设计模式**。让我们看看这种模式在野外的几个例子。

### multi_json

一个很好的例子是用最快的后端解析 json 的 [multi_json](https://github.com/intridea/multi_json) gem。在`multi_json`中，每个后端都包含在一个从`Adapter`派生的类中。这里是`multi_json/lib/multi_json/adapters/gson.rb`。

```
require 'gson'
require 'stringio'
require 'multi_json/adapter'

module MultiJson
  module Adapters
    # Use the gson.rb library to dump/load.
    class Gson < Adapter
      ParseError = ::Gson::DecodeError

      def load(string, options = {})
        ::Gson::Decoder.new(options).decode(string)
      end

      def dump(object, options = {})
        ::Gson::Encoder.new(options).encode(object)
      end
    end
  end
end 
```

这里，`load`执行每个库将 JSON 字符串转换成对象的方法，`dump`执行将对象转换成字符串的方法。

### ActiveRecord

ActiveRecord 是 Rails 的 ORM 库，用于与关系数据库交互。它依赖于适配器模式，允许开发人员使用相同的方法与任何受支持的数据库进行交互。我们可以在`ActiveRecord`的[连接适配器](https://github.com/rails/rails/tree/master/activerecord/lib/active_record/connection_adapters)中找到这种模式。

```
module ActiveRecord
  module ConnectionAdapters # :nodoc:
    extend ActiveSupport::Autoload

    autoload :Column
    autoload :ConnectionSpecification

    autoload_at 'active_record/connection_adapters/abstract/schema_definitions' do
      autoload :IndexDefinition
      autoload :ColumnDefinition
      autoload :ChangeColumnDefinition
      autoload :ForeignKeyDefinition
      autoload :TableDefinition
      autoload :Table
      autoload :AlterTable
      autoload :ReferenceDefinition
    end

    autoload_at 'active_record/connection_adapters/abstract/connection_pool' do
      autoload :ConnectionHandler
      autoload :ConnectionManagement
    end

    autoload_under 'abstract' do
      autoload :SchemaStatements
      autoload :DatabaseStatements
      autoload :DatabaseLimits
      autoload :Quoting
      autoload :ConnectionPool
      autoload :QueryCache
      autoload :Savepoints
    end

    ...

    class AbstractAdapter
      ADAPTER_NAME = 'Abstract'.freeze
      include Quoting, DatabaseStatements, SchemaStatements
      include DatabaseLimits
      include QueryCache
      include ActiveSupport::Callbacks
      include ColumnDumper

      SIMPLE_INT = /\A\d+\z/

      define_callbacks :checkout, :checkin

      attr_accessor :visitor, :pool
      attr_reader :schema_cache, :owner, :logger
      alias :in_use? :owner

      ...

      attr_reader :prepared_statements

      def initialize(connection, logger = nil, config = {}) # :nodoc:
        super()

        @connection          = connection
        @owner               = nil
        @instrumenter        = ActiveSupport::Notifications.instrumenter
        @logger              = logger
        @config              = config
        @pool                = nil
        @schema_cache        = SchemaCache.new self
        @visitor             = nil
        @prepared_statements = false
      end

      ... 
```

`ActiveRecord`包括很多适配器，包括 MySQL 和 PostgreSQL [这里的](https://github.com/rails/rails/tree/master/activerecord/lib/active_record/connection_adapters)。浏览其中的几篇文章，看看这种模式的优秀例子。

### 莫内塔

我最喜欢的宝石之一是 moneta，它是一个键值存储的统一接口，比如 Redis。下面是一个使用文件作为键值存储的示例:

```
require 'moneta'

# Create a simple file store
store = Moneta.new(:File, dir: 'moneta')

# Store some entries
store['key'] = 'value'

# Read entry
store.key?('key') # returns true
store['key'] # returns 'value'

store.close 
```

从用户的角度来看，访问`redis`和`daybreak`就像读取或修改散列一样简单。下面是`daybreak`适配器的样子(为了节省空间，去掉了注释):

```
require 'daybreak'

module Moneta
  module Adapters
    class Daybreak < Memory
      def initialize(options = {})
        @backend = options[:backend] ||
          begin
            raise ArgumentError, 'Option :file is required' unless
options[:file]
            ::Daybreak::DB.new(options[:file], serializer:
::Daybreak::Serializer::None)
          end
      end

      def load(key, options = {})
        @backend.load if options[:sync]
        @backend[key]
      end

      def store(key, value, options = {})
        @backend[key] = value
        @backend.flush if options[:sync]
        value
      end

      def increment(key, amount = 1, options = {})
        @backend.lock { super }
      end

      def create(key, value, options = {})
        @backend.lock { super }
      end

      def close
        @backend.close
      end
    end
  end
end 
```

## 创建适配器 Gem

让我们制作一个 gem，允许用户为一个基本的 CSV 解析器选择一个适配器。下面是我们的文件夹结构的样子:

```
├── Gemfile
├── Rakefile
├── lib
│   ├── table_parser
│   │   └── adapters
│   │       ├── scan.rb
│   │       └── split.rb
│   └── table_parser.rb
└── test
    ├── helper.rb
    ├── scan_adapter_test.rb
    ├── split_adapter_test.rb
    └── table_parser_test.rb 
```

## 属国

将`minitest`和`ruby "2.3.0"`添加到**gem 文件**中:

```
# Gemfile
source "https://rubygems.org"

ruby "2.3.0"

gem "minitest", "5.8.3" 
```

Ruby 2.3 增加了新的 [squiggly heredoc](http://devblog.avdi.org/2016/01/06/about-the-ruby-squiggly-heredoc-syntax/) 语法，这在这种情况下很有用，因为它防止了不必要的前导空格。将它添加到 **Gemfile** 不会安装它。它需要单独安装，命令如下(如果您使用 RVM):

```
$ rvm install 2.3.0 
```

## 测试支持

添加一个 **Rakefile** ，让我们使用`rake`来运行我们所有的测试:

```
# Rakefile
require "rake/testtask"

Rake::TestTask.new do |t|
  t.pattern = "test/*_test.rb"
  t.warning = true
  t.libs << 'test'
end

task default: :test 
```

`t.libs << 'test'`在运行任务时将**测试**文件夹添加到我们的`$LOAD_PATH`中。默认包含 **lib** 文件夹。

## 主模块

**lib/table_parser.rb** 将实现用户在使用 gem 时访问的内容:

```
# lib/table_parser.rb

module TableParser
  extend self

  def parse(text)
    self.adapter.parse(text)
  end

  def adapter
    return @adapter if @adapter
    self.adapter = :split
    @adapter
  end

  def adapter=(adapter)
    require "table_parser/adapters/#{adapter}"
    @adapter = TableParser::Adapter.const_get(adapter.to_s.capitalize)
  end
end 
```

`::adapter`第一次调用时设置默认适配器。注意**适配器在设置**之前不会加载。这避免了开发人员暴露于未使用的适配器中的错误，并允许同一项目中的适配器使用它们自己的依赖项，而无需预先要求所有适配器的所有依赖项。

## 适配器

第一个适配器使用带有适当正则表达式的`scan`方法进行解析。正则表达式分隔符搜索不是逗号或两个连续逗号的任何内容:

```
# lib/table_parser/adapters/scan.rb

module TableParser
  module Adapter
    module Scan
      extend self

      def parse(text)
        delimiter = /[^,]+|,,/
        lines = text.split(/\n/)

        keys = lines.shift.scan(delimiter).map { |key| key.strip }

        rows = lines.map do |line|
          row = {}
          fields = line.scan(delimiter)
          keys.each do |key|
            row[key] = fields.shift.strip
            row[key] = "" if row[key] == ",,"
          end
          row
        end

        return rows
      end
    end
  end
end 
```

第二个适配器通过另一个正则表达式使用`split`方法进行解析:

```
# lib/table_parser/adapters/split.rb

module TableParser
  module Adapter
    module Split
      extend self

      def parse(text)
        delimiter = / *, */
        lines = text.split(/\n/)
        keys = lines.shift.split(delimiter, -1)

        rows = lines.map do |line|
          row = {}
          fields = line.split(delimiter, -1)
          keys.each { |key| row[key] = fields.shift }
          row
        end

        return rows
      end
    end
  end
end 
```

## 测试助手

这里需要做的主要事情是设置`minitest`依赖项，我们也可以继续加载项目代码。这不是一个真正必要的文件，但是在大型项目中很常见。

```
# test/test_helper.rb
require "minitest/autorun"
require "table_parser" 
```

## 共享测试示例

我们应该避免为每个适配器重复所有的测试。相反，我们将编写从更简单的适配器测试文件中提取的**共享示例**:

```
# test/table_parser_test.rb
require "test_helper"

module TableParserTest
  def test_parse_columns_and_rows
    text = <<~TEXT
      Name,LastName
      John,Doe
      Jane,Doe
    TEXT

    john, jane = TableParser.parse(text)

    assert_equal "John", john["Name"]
    assert_equal "Jane", jane["Name"]

    assert_equal "Doe", john["LastName"]
    assert_equal "Doe", jane["LastName"]
  end

  def test_empty
    text = <<~TEXT
      Name,LastName
    TEXT

    result = TableParser.parse(text)

    assert_equal [], result
  end

  def test_removes_leading_and_trailing_whitespace
    text = <<~TEXT
      ,  Name,LastName
      ,John    ,  Doe
      ,     Jane,       Doe
    TEXT

    john, jane = TableParser.parse(text)

    assert_equal "John", john["Name"]
    assert_equal "Jane", jane["Name"]

    assert_equal "Doe", john["LastName"]
    assert_equal "Doe", jane["LastName"]
  end
end 
```

## 适配器测试文件

接下来，对于每个适配器，我们需要一个测试来运行共享的例子。因为测试示例在一个单独的文件中，并且是共享的，所以这些非常紧凑。

首先，一个用于扫描适配器:

```
# test/scan_adapter_test.rb
require "table_parser_test"

class TableParser::ScanAdapterTest < Minitest::Test
  include TableParserTest

  def setup
    TableParser.adapter = :scan
  end
end 
```

接下来，对于拆分适配器:

```
# test/split_adapter_test.rb
require "table_parser_test"

class TableParser::SplitAdapterTest < Minitest::Test
  include TableParserTest

  def setup
    TableParser.adapter = :split
  end
end 
```

## 运行测试套件

由于有了 **Rakefile** ，验证两个适配器都工作很容易；

```
$ rake

Run options: --seed 26993

# Running:

......

Fabulous run in 0.001997s, 3004.7896 runs/s, 9014.3689 assertions/s.

6 runs, 18 assertions, 0 failures, 0 errors, 0 skips 
```

## 结论

适配器是整合完成某件事情的多种方式的好方法，而不需要求助于堆积如山的条件分支。它们还允许您将方法分成独立的库，这些库可以有自己的依赖项。如果适配器是以惰性方式加载的，那么损坏的适配器不会影响项目，除非它们被使用。

请继续关注更多优秀的设计模式文章！

## 分享这篇文章