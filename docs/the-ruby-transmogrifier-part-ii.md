# 红宝石变形人，第二部分

> 原文：<https://www.sitepoint.com/the-ruby-transmogrifier-part-ii/>

![Input Output Graphic](img/9c7dece3f3cf0d1d7a864e42bf0e31e3.png)

## 将信息输入变形器

在上一集中，我们将数据从一种格式转换成另一种格式。现在你需要用变形器把数据输入其中。我们可以在那里对文件名进行硬编码，但那会回来困扰我们。让我们创建 is，这样我们就可以加载定义文件、数据文件和输出文件的名称。

您需要读取 csv 数据文件。在 Ruby 中如何一行一行地读取一个文件？

```
require 'csv'

File.open("def.txt") do |file|
  while line = file.gets
    puts line
  end
end
```

您可以保存文件。我把它保存为 transmogrifier.rb

你可以继续运行它。

```
$ ruby transmogrifier.rb
FIELD NAME      FORMAT
NAME            A(50)
ADDRESS1        A(50)
ADDRESS2        A(50)
CITY            A(50)
STATE           A(2)
ZIP             A(10)
CONTACT         A(50)
CONTACTPHONE    A(10)
ACCOUNTOPENED   9(8)
```

它工作了。我们现在有数据，我们需要加载定义。在 transmogrifier.rb 中，继续添加代码来完成这项工作。记得该怎么做？

```
File.open("data.csv") do |file|
  while line = file.gets
    puts line
  end
end
```

如果你很好奇，就去运行它吧。

既然您已经加载了定义和数据，那么您需要将它们发送到 transmogrifier。您需要发送数据、字段长度、类型和列名。四个中的三个来自定义文件。

让我们写出类型、长度和字段名。字段名从一行的开头开始
我们可以把这一行分开，放入一个数组中。因为文件使用空格而不是制表符来对齐，所以我们可以使用 split。

```
require 'csv'

definitions = Array.new

File.open("def.txt") do |file|
  while line = file.gets
    definitions = line.split()
    puts definitions[0]
    puts definitions[1]
  end
end
...
```

这给了我们字段名，但类型和长度仍然在一起。你在考虑正则表达式来寻找类型和长度吗？

```
require 'csv'

definitions = Array.new

File.open("def.txt") do |file|
  while line = file.gets
    definitions = line.split()
    field = definitions[0]
    type = definitions[1] =~ /9/? '9' : 'A'
    length = definitions[1].slice(1..definitions[1].length).gsub(/[^0-9]/, "")
    puts 'field => ' + field.upcase + ' type => ' + type + ' length => ' + length.to_s
  end
end
```

继续跑吧。

```
$ ruby transmogrifier.rb
field => FIELD type => A length =>
field => NAME type => A length => 50
field => ADDRESS1 type => A length => 50
field => ADDRESS2 type => A length => 50
field => CITY type => A length => 50
field => STATE type => A length => 2
field => ZIP type => A length => 10
field => CONTACT type => A length => 50
field => CONTACTPHONE type => A length => 10
field => ACCOUNTOPENED type => 9 length => 8
```

现在我们有进展了。传递给变形器的四个变量中的三个。狂笑。

## 一个问题

您认为如何将数据放入该阵列？

如果我们把数组扔进 hash 里呢？然后，将这个散列放到另一个散列中，其中键是字段名，数据来自第一个散列。

我们所要做的就是遍历数据，从该列的散列中找到密钥，然后将所有这些发送给 transmogrifier。让事情变得简单。

首先，将定义数据放入一个散列中。然后把它塞进杂凑里。为了好玩，继续输出最后一个散列以便我们可以看到它。

```
require 'csv'

object_name = Hash.new
definitions = Array.new

File.open("def.txt") do |file|
  while line = file.gets
    definitions = line.split()
    field = definitions[0]
    type = definitions[1] =~ /9/? '9' : 'A'
    length = definitions[1].slice(1..definitions[1].length).gsub(/[^0-9]/, "")
    object_formatting = Hash.new
      object_formatting["type"] = type
      object_formatting["length"] = length
      object_formatting["field"] = field
      object_name[field.upcase] = object_formatting
  end
end
puts object_name
```

运行它，让我们看看我们有什么。

```
$ ruby transmogrifier.rb
{"FIELD"=>{"type"=>"A", "length"=>"", "field"=>"FIELD"}, "NAME"=>{"type"=>"A", "length"=>"50", "field"=>"NAME"}, "ADDRESS1"=>{"type"=>"A", "length"=>"50", "field"=>"ADDRESS1"}, "ADDRESS2"=>{"type"=>"A", "length"=>"50", "field"=>"ADDRESS2"}, "CITY"=>{"type"=>"A", "length"=>"50", "field"=>"CITY"}, "STATE"=>{"type"=>"A", "length"=>"2", "field"=>"STATE"}, "ZIP"=>{"type"=>"A", "length"=>"10", "field"=>"ZIP"}, "CONTACT"=>{"type"=>"A", "length"=>"50", "field"=>"CONTACT"}, "CONTACTPHONE"=>{"type"=>"A", "length"=>"10", "field"=>"CONTACTPHONE"}, "ACCOUNTOPENED"=>{"type"=>"9", "length"=>"8", "field"=>"ACCOUNTOPENED"}}
...
```

很可爱。现在您需要处理数据文件。让我们获取该列的字段名和数据

```
CSV.foreach("data.csv", headers: true) do |data|
  data.headers.each do |field|
    puts field + ' => ' + data[field].to_s
  end
end
```

坚持住！CVS.foreach()调用是什么？让我们[看看那个](http://ruby-doc.org/stdlib-1.9.3/libdoc/csv/rdoc/CSV.html)。
该行将处理一个 CSV 文件，并将第一行视为标题而非数据。

当你运行时，你的输出应该是这样的

```
name => Wonder widgets
address1 => 1600 Vassar Street
address2 =>
city => dallas
state => tx
zip => 75220
contact => Tim Smith
contactPhone => 214-555-1212
accountOpened => 12052001

name => Timmy's Bikes
address1 => 2723 Auburn Street
address2 => Building 3
city => Erie
state => PA
zip => 16508-1234
contact =>
contactPhone => 814-555-4321
accountOpened => 865289
```

## 我们快到家了。

现在我们有了数据和定义。让我们把信息发送给变形人。

当我们遍历数据时，我们需要从我们之前创建的散列中找到匹配的键。当我们匹配时，把它发送给变形人

```
CSV.foreach("data.csv", headers: true) do |data|
  data.headers.each do |field|
    object_name.each do |key,value|
      if field.upcase == key
        field.upcase!
        line = transmogrifier(data[field].to_s, object_name[field]["length"].to_i, object_name[field]["type"], field)
        print line
      end
    end
  end
  puts ' '
  puts ' --------- '
end
```

那将完成我们谈论的所有事情。如果我们像第一部分那样编写测试，我们会发现很多问题。如果一列没有数据怎么办？我们如何在一行中获得所有数据？我们如何保存数据？我们如何传入定义和数据文件名？

这是我在重构之前想到的

```
require 'csv'

def transmogrifier(data,len,type,column)

proper_formatted = ''

unless data.nil?
    case
    when data.length > len
      data = data.slice(0..(len-1))
    when data.length < len
      if type == "A"
        data = data.ljust(len)
      else
        data = data.rjust(len)
      end
    else
      if column == "ACCOUNTOPENED"
        data = data.slice(4..7)+data.slice(0..3)
      else
        data
      end
    end
    proper_formatted += data
  end
  proper_formatted += '|' # added pipes to see where the field ends
  proper_formatted
end

object_name = Hash.new

file = File.new(ARGV[0],"r")

definitions = Array.new

while (line = file.gets)
    definitions = line.split()
    field = definitions[0]
    type = definitions[1] =~ /9/? '9' : 'A'
    length = definitions[1].slice(1..definitions[1].length).gsub(/[^0-9]/, "")
    object_formatting = Hash.new
      object_formatting["type"] = type
      object_formatting["length"] = length
      object_formatting["field"] = field
      object_name[field.upcase] = object_formatting
  end
file.close

aFile = File.new(ARGV[2], "w")

CSV.foreach(ARGV[1], headers: true) do |data|
  data.headers.each do |field|
    object_name.each do |key,value|
      if field.upcase == key
        field.upcase!
        line = transmogrifier(data[field].to_s, object_name[field]["length"].to_i, object_name[field]["type"], field)
        aFile.write(line)
        print line  #so you can see something in the terminal window
      end
    end
  end
  aFile.write("n")
  puts ' '
  puts ' --------- '
end

aFile.close
```

如果您运行`$ ruby transmogrifier.rb def.txt data.csv formated.txt`
,您应该在您正在工作的文件夹中有一个名为 formated.txt 的新文件

现在向前去，传达疯狂的笑声。

## 分享这篇文章