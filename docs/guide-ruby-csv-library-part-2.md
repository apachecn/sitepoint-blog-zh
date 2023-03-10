# Ruby CSV 库指南，第二部分

> 原文：<https://www.sitepoint.com/guide-ruby-csv-library-part-2/>

![65vHPcY](img/7848d8522d215776b1000b206ebdc10d.png)

干得好，儿子！–鲍勃叔叔(如果你不清楚鲍勃叔叔是谁，你可能想读一下本教程的第一部分[)](https://www.sitepoint.com/guide-ruby-csv-library-part/)

鲍勃叔叔对我们迄今为止所做的工作很满意。但是还有一件事…还记得这个文件吗？

![](img/07b60ae65062ffc26bc7a484737fc34e.png)

新员工很困惑，每一栏是什么意思？34 或 2548 是多少？我们需要做的是将**头**添加到 CSV 文件中。让我们更新我们的文件(下面是上面图片的纯文本格式，包括标题):

```
Name,Times arrived,Total $ spent,Food feedback
Dan,34,2548,Lovin it!
Maria,55,5054,"Good, delicious food"
Carlos,22,4352,"I am ""pleased"", but could be better"
Stephany,34,6542,I want bigger steaks!!!!!
```

现在，让我们把文件导入 Ruby(假设它的名字是‘guests . CSV’):

```
require 'csv' # since CSV is part of the standard library, we do need to 'require' it, remember?
guests = CSV.read('guests.csv') # read the entire file into a 'guests' variable
```

CSV 库不具有魔力，所以它不知道这里的第一行是一个标题。因此，如果我们运行上面的代码,“guests”变量将是一个由 5 个数组元素组成的双数组，第一个元素是“header ”,就像其他行一样:

```
["Name", "Times arrived", "Total $ spent", "Food feedback"]
```

让我们告诉 CSV 库不要像对待其他行一样对待第一行。我们如何做到这一点？请记住，每个在 Ruby 中生成“类似 csv”的 CSV 方法都可以被赋予一个*选项*(我们在第一部分中使用了*转换器:数字*)列表，它基本上指示库以不同于默认的方式处理文件(或字符串)。在众多选项中，有一个选项可以让 CSV 库知道第一行是一个标题，即 *:headers = > true* 。让我们通过添加以下选项来修改上面两行代码中的第二行:

```
guests = CSV.read('guests.csv', headers:true)
```

所以，你可能会想，现在有什么不同？让我们试着检查一下`guests`变量当前包含了什么(提示:它不再是双数组了！):

```
#<CSV::Table mode:col_or_row row_count:5>
```

现在不要担心所有这些意味着什么，我们将在下面一步步解释它。

## 带有头文件的文件是特殊的

我们的`guests`变量现在是一个 *CSV::Table* 类型的对象，而不是一个(double) *数组*。您可能知道，Ruby 中的每个对象都有自己独立的方法。CSV::Table 也是如此。

幸运的是，这个新的对象类型的行为与我们的 double 数组非常相似。就像您可以迭代 double 数组来获得带有`each`的每一行一样，您可以对`CSV::Table`使用相同的方法来实现相同的事情:

```
guests.each do |guest_row|
  p guest_row #<CSV::Row "Name:"Dan"...
end
```

您应该看到 4 行输出(是的，是 4 行，而不是 5 行，它现在认为我们的第一行是一个实际的标题)。以下是前两行:

```
#<CSV::Row "Name":"Dan" "Times arrived":"34" "Total $ spent":"2548" "Food feedback":"Lovin it!">
#<CSV::Row "Name":"Maria" "Times arrived":"55" "Total $ spent":"5054" "Food feedback":"Good, delicious food">
```

如您所见，您现在得到的不是一堆数组，而是一个新的`CSV::Row`。顾名思义，这些对象中的每一个都引用了一行。

基本上，如果我们包括`headers:true`，我们得到:

*   一个`CSV::Table`而不是一个双数组
*   `CSV::Row`代表行的对象。

如果要取回双数组，只需调用`CSV::Table#to_a`方法即可。让我们看看会发生什么:

```
guests = CSV.read('guests.csv', headers:true) #=> <CSV::Table mode:col_or_row row_count:5>
p guests.to_a # will output...
#[["Name", "Times arrived", "Total $ spent", "Food feedback"], ["Dan", "34", "2548", "Lovin it!"]...........
```

您也可以使用`#to_s`而不是`to_a`来获得文件的字符串表示:

```
p guests.to_s
#=> "Name,Times arrived,Total $ spent,Food feedback\nDan,34,2548.........
```

## 为什么是不同的对象？

为每个 CSV 行使用这些奇怪的`CSV::Row`类型的对象而不是一个好的 ol '数组有什么意义呢？让我们以 *guests.csv* 文件中第二排的丹为例。在没有任何选项的情况下，Dan 在 Ruby 中将被表示为这种格式:

```
["Dan", "34", "2548", "Lovin it!"]
```

当我们添加`headers:true`选项时，情况发生了变化:

```
#<CSV::Row "Name":"Dan" "Times arrived":"34" "Total $ spent":"2548" "Food feedback":"Lovin it!">
```

假设您只想打印包含客人姓名的 *guests.csv* 的第一列。如果 Dan 被表示为一个普通的数组，您将不得不调用`dan_array[0]`来获取数据。我们的代码应该是这样的(这次我将使用`foreach`方法而不是`read`方法来读取 CSV 文件):

```
guests = CSV.foreach('guests.csv') do |row|
  puts row[0]
end #=> outputs "Name", "Dan", "Maria", "Carlos", "Stephany" on separate lines
```

如果其他人正在阅读你的代码，他可能会想知道`row[0]`是什么意思。此外，我们将“Name”作为输出的第一行，这肯定不是我们想要的。我们想打印客人的名字，但不要标题！更好的方法是，将标题用作指向行中该列下特定单元格的指针。见证`CSV::Row`的力量:

```
guests = CSV.foreach('guests.csv', headers:true) do |row|
  puts row['Name'] # For each row, give me the cell that is under the 'Name' column
end #=> outputs "Dan", "Maria", "Carlos", "Stephany" on separate lines. "Name" is not printed.
```

完美！如你所见，它有自己方便的方法。在这种情况下，第二行只是`row.[]('Name')`的语法糖，它将遍历所有行，只输出“Name”列下的那些单元格。您可以将“名称”替换为“到达时间”、“总花费”或“食物反馈”，以从其他列中获取相应的值。需要记住的一件重要事情是，标题名称是区分大小写的。这是行不通的:

```
guests = CSV.foreach('guests.csv', headers:true) do |row|
  puts row['Food Feedback'] #=> Will print nil for all rows, the correct column name is 'Food feedback'
end
```

如果我必须描述`CSV::Row`，我会把它描述为一个数组和一个散列的私生子。与数组不同，您可以通过名称引用它的元素，并且与散列不同，您可以拥有重复的“键”，我们将在后面看到。

如果我们想将另一个项目推送到`CSV::Table`对象，我们可以使用与常规数组相同的方法(或者是`push`或者是 `) with an array as the argument. To get only the headers, we use the` `headers`方法)。以下是一些例子:

```
guests = CSV.read('guests.csv',headers:true) #<CSV::Table mode:col_or_row row_count:5>
guests << ['Eve', 24, 54, 'Delicious'] #<CSV::Table mode:col_or_row row_count:6>
print guests.headers #=> ["Name", "Times arrived", "Total $ spent", "Food feedback"]
```

`CSV::Table#delete`方法将从`CSV::Table`对象中删除整个列，并将删除的条目作为数组返回。

```
guests.delete('Name') #=> returns ["Dan", "Maria", "Carlos", "Stephany"]
```

如果我们想删除一行而不是一个数组呢？我们可以使用相同的`.delete`方法，这一次，提供一个数字索引(从 0 开始，所以 0 =第一行，1 =第二行)。与上面的例子一样，返回值将是被删除的行。

```
guests.delete(0)
# This method returns #<CSV::Row "Name":"Dan" " Times arrived":"34" "Total $ spent":"2548" "Food feedback":"Lovin it!">
```

如果你熟悉数组和散列，事情会变得有点奇怪。当变量中有`CSV::Table`时，使用列名作为索引来访问列中的值，使用从 0 开始的数值来访问行(0-第一行，1-第二行，依此类推)。):

```
guests = CSV.read('guests.csv', headers:true)
guests['Times arrived'] #=> ["34", "55", "22", "34"]
guests[0] #=> #<CSV::Row "Name":"Carlos" "Times arrived":"22" "Total $ spent":"4352" "Food feedback":"I am \"pleased\", but could be better">
```

我们甚至可以将这两种表示法结合起来，只访问一个特定的单元格:

```
guests['Times arrived'][1] #=> Returns '55'
guests[1]['Times arrived'] #=> Also returns 55
```

您用这段代码告诉 Ruby 的是:“给我第二行“到达时间”列下的单元格的值”。整洁！

## CSV 标题的实际使用

让我们尝试使用我们的 *guests.csv* 文件并更改“总花费金额”列，使其包含小数而不是整数:

```
new_guests_csv = [] # We create an array to gold the new CSV data
CSV.foreach('guests.csv',headers:true) do |guest| # Iterate over each row of our CSV file
  guest['Total $ spent'] = guest['Total $ spent'].to_f #
  new_guests_csv << guest # Add the new row into new_guests_csv
end
```

我决定使用`.foreach`方法来读取这里的文件。我也可以使用`CSV#read`:

```
new_guests_csv  = [] # We create an array to gold the new CSV data
old_guests_csv = CSV.read('guests.csv', headers:true) # Reads the entire content of the CSV into the variable
old_guests_csv.each do |guest| # old_guests_csv is CSV::Table object which has #each method to iterate over its rows
  guest['Total $ spent'] = guest['Total $ spent'].to_f # Same thing as with our previous code
  new_guests_csv << guest # Add the new row into new_guests_csv
end
```

如果我们想将数据保存在一个新文件中，我们可以这样做:

```
CSV.open('updated_guests.csv', 'w') do |csv| # Create a new file updated_guests.csv
  csv << ['Name', 'Times arrived', 'Total $ spent (decimals)', 'Food feedback'] # Add new headers 
  new_guests_csv.each do |row| 
    # Since we now have the entire updated CSV file in this variable as a double array,
    # we iterate over each (array) element

    csv.puts row
  end
end
```

通常，当向一个新的 CSV 文件写入数据时，您会想要更改您的头名称。大多数人所做的是，首先，添加新的标题(就像我们在上面第二行所做的那样)，然后，遍历修改后的变量(在我们的例子中是`new_guests_csv`)并分别添加每一行。

**快速提示:**如果一个文件有两个相似的列名会怎么样？假设我们的 *guests.csv* 文件包含另一个“姓名”列，我们如何访问它？正如您已经知道的，第一列可以用`row_object['Name']`来访问。要访问第二个，写`row_object['Name', 2]`

## 如何在不丧失理智的情况下阅读一个大的 CSV 文件

Ruby 是一种主要针对人而非计算机优化的语言。因此，性能是第二位的，方便是第一位的。不幸的是，当您尝试将一个大文件读入内存时，这可能会成为一个问题。如果你的文件是 300 兆字节，而运行你的代码的机器有，比如说，512 兆的内存，就会有问题。

让我们回到鲍勃叔叔。快进到未来，鲍勃大叔有一个专营权和数百万美元的业务。他仍然保存着他的 CSV 文件，尽管现在已经有超过 70，000 行了！如果我们试图用`CSV.read`(将整个文件读入内存)来读取和操作这个文件，我们在 ram 中的程序大小将增加几倍。发生这种情况是因为内存中有一个对象(如果我们有头的话，是一个`CSV::Row`的数组)用于 CSV 文件的每一行。那是 70.000 个物体！假设我们只想返回那些到达餐馆超过 10 次的人的行:

```
#...doing some regular stuff, the program memory size is 12MB
guests = CSV.read('guests.csv',headers:true) # a big CSV file is read into memory, the size is now over 100MB!

guests.select do |row| 
  row['Times arrived'].to_i > 10  # if we put the return value of the select block in another variable, we'll have even bigger size
end
```

当然，我们可以通过使用 CSV.foreach 来避免这种情况，它不是将整个文件读入内存，而是逐行迭代，并且在完成迭代后不会影响整个程序的内存大小。但是这里有一个小问题，我们必须重新构建我们的程序:

```
guests_who_visited_more_than_ten_times = Array.new
CSV.foreach('guests.csv', headers:true) do |guest|
  guests_who_visited_more_than_ten_times << guest if guest['Times arrived'] > 10
end
```

在这里，我们用表达性来换取效率，第一个从内存中读取的例子更好，因为我们的数据是我们可以使用`Array#select`的实际对象。幸运的是，你可以两全其美，使用一个枚举器来节省内存和表达能力(我从 [RubyTapas](http://rubytapas.com) 那里学到了这个令人敬畏的技巧):

```
CSV.open('guests.csv', headers:true) do |guest|
  guests = guest.each
  guests.select do |row| 
    row['Times arrived'].to_i > 10
  end
end
```

如果你不熟悉第二行，我们基本上创建了一个**枚举器**对象，并将其存储到`guests`变量中。枚举器允许我们“按需”迭代，比方说，如果我有从 1 数到 100 的代码:

```
1.upto(10) { |x| p x }
```

将它转换成一个枚举器对象将允许我，例如，在我的程序的一部分中从 1 数到 5，然后，稍后，完成数到 10:

```
enum = 1.upto(10)
p enum.next #=> 1
p 'bunch of other code here'
p enum.next #=> 2
p 'and here'
p enum.next #=> 3
```

枚举器的“随需应变”特性使它们对内存友好。与使用`CSV.read`的第一个例子相比，我们不会有成千上万的对象进入 RAM，并且与`CSV.foreach`不同，我们不必通过修改逻辑来重新分解代码。

好了，这就是 Ruby CSV 系列的结束。希望你学到了很多！

## 分享这篇文章