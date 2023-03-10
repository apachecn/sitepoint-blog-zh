# 如何在 R 中导入数据和导出结果

> 原文：<https://www.sitepoint.com/how-to-import-data-and-export-results-in-r/>

随着对“大”数据的狂热，分析工具开始流行。其中一个工具是编程语言 [R](https://www.r-project.org/) 。在本指南中，我将展示如何从文本文件、CSV 文件和数据库中提取数据。然后我将展示如何将数据发送到 web 服务器。

你可能想知道，我需要重新学习一门新的语言吗？答案是否定的！您需要知道的只是几个命令。

来自不同背景、使用各种编程语言开发 web 应用程序的程序员可以将数据导入 R，并在处理后以他们需要的格式导出。

*注:如果你不熟悉 R，我推荐 SitePoint 关于[如何安装 R 和 RStudio](https://www.sitepoint.com/introduction-r-rstudio/) 的文章。它提供了 R 中的基本命令和对该语言的一般性介绍。本文介绍了无需使用 RStudio IDE 就可以在 R 终端上运行的命令。然而，对于初学者来说，在终端上处理大型数据集可能会很困难，所以我建议使用 RStudio 来丰富体验。在 RStudio 中，您可以在控制台中运行相同的命令。*

## 处理文本文件

使用稍微修改的`read.table`命令可以读取本地机器上的文本文件。因为它是为读取表格而设计的，所以您可以将分隔符设置为空字符串(`""`)来逐行读取文本文件:

```
file_contents = read.table("<path_to_file>", sep = "") 
```

*注意:在你看到尖括号的地方，比如在`<path_to_file>`中，插入必要的数字、标识符等。没有括号。*

文件的路径也可以是文件的相对路径。如果您的行长度不等，您也必须设置`fill = TRUE`。该命令的输出是 R 中的一个[数据帧。](http://www.r-tutor.com/r-introduction/data-frame)

如果您的文件太大，无法一次读取，您可以尝试使用`skip`和`nrow`选项分步读取。例如，要读取文件中的第 6–10 行，请运行以下命令:

```
connection <- file("<path_to_file>")
lines6_10 = read.table(connection, skip=5, nrow=5) # 6-10 lines 
```

## 处理 CSV 文件

一个 **CSV** (逗号分隔值)文件是这样一个文件，从字面上看，它包含了用逗号分隔的值。您可以使用`read.csv`命令读取 CSV 文件:

```
file_contents = read.csv("<path_to_file>") 
```

一个`header`选项表明 CSV 文件是否包含列标题。默认设置为`TRUE`。(这也可以在读取文本文件时指定。)如果不同行中的列不相等，也需要将`fill`设置为`TRUE`。

对于大文件，可以用类似的方式跳过行:

```
connection <- file("<path_to_file>")
lines6_10 = read.csv(connection, skip=5, nrow=5) # 6-10 lines 
```

## 使用 MySQL 数据库

要建立数据库连接，您需要单独的`RMySQL`库。可以使用以下命令安装它:

```
install.packages('RMySQL') 
```

安装后，您需要通过运行以下命令来激活它:

```
library('RMySQL') 
```

假设您的数据库正在运行，您现在可以在建立连接后进行 MySQL 查询:

```
con <- dbConnect(MySQL(),
  user="root", password="root",
  dbname="nsso", host="localhost", port=8889) 
```

如果你在 Mac 上通过 MAMP 运行 MySQL，你需要指定一个`unix.socket`:

```
con <- dbConnect(..., unix.socket = "/Applications/MAMP/tmp/mysql/mysql.sock") 
```

要进行 MySQL 查询，首先需要执行查询，然后将数据存储在数据框中:

```
rs <- dbSendQuery(con, "SELECT * FROM my_table;") # Make sure you run a LIMIT if your query is too large
data <- fetch(rs, n = -1) 
```

完成查询后，您可以通过`dbDisconnect`命令断开连接:

```
dbDisconnect(con) 
```

## 在网上阅读数据

如果你的数据源在网上呢？你如何阅读在线文件？在 R 中，只需更改 read 命令中指定的文件路径就可以做到这一点。您需要使用`url`命令并在`read.csv`命令中指定 URL。例如:

```
file_contents = read.csv(url("<file_URL>")) 
```

对于一个数据库，`host`可能被更改为从 web 服务器上的数据库中提取数据。

## 导出数据

就像`read.csv`和`read.table`一样，可以使用`write`命令将数据帧导出为文本或 CSV 文件:

```
write.csv(data_frame, file = "data.csv") 
```

要使用不同的分隔符(比如制表符)导出为文本文件，可以使用`write.table`命令:

```
write.table(data_frame, file = "data.txt", sep = "\t") 
```

更新数据库同样简单，可以通过执行`UPDATE`和`INSERT` MySQL 命令来完成。

## 导出图表

一旦你在 R 中处理并绘制了数据，你也可以导出它！`png`或`jpeg`命令可以帮你做到这一点。基本上，它保存当前活动的图:

```
# Initiate Image
png(filename="sample.png")
# Make a plot
plot(c(1,2,3,4,5), c(4,5,6,7,8))
# Save the plot
dev.off() 
```

理想情况下，您可以调整第二个命令来保存所需的绘图。

## 将数据导出到 Web

将文件直接上传到网站可能有点棘手，但是您可以使用两个步骤将数据导出到网站:在本地保存文件，然后将其上传到网站。您可以通过 R 使用`POST`请求将文件上传到 web，这可以使用`httr`包来模拟:

```
POST("<upload_URL>", body = list(name="<path_to_local_file>", filedata = upload_file(filename, "text/csv"))) 
```

更多细节，这里有[一个关于`httr`包](https://cran.r-project.org/web/packages/httr/vignettes/quickstart.html)的快速启动指南。

## 结论

近年来，r 在从事统计工作的人群中非常受欢迎，现在是学习这种奇妙语言的好时机。它足够灵活，可以与各种类型的数据源同步，而且使用 R 也非常容易，与您的背景无关。希望这篇文章能让你从 R 开始！

## 分享这篇文章