# 使用 Python 解析电子表格数据

> 原文：<https://www.sitepoint.com/using-python-parse-spreadsheet-data/>

大型组织和企业通常将数据存储在电子表格中，需要一个界面将这些数据输入到他们的 web 应用程序中。总体思路是上传文件，读取其内容，并将其存储在 web 应用程序使用的文件或数据库中。组织可能还需要从 web 应用程序中导出数据。例如，他们可能需要导出班级中所有学生的成绩。同样，电子表格是首选媒介。

在本帖中，我们将讨论处理这些文件的不同方法，并使用 Python 解析它们以获得所需的信息。

## 快速电子表格入门

在解析电子表格之前，您必须了解它们的结构。电子表格文件是工作表的集合，每个工作表是放置在网格中的数据单元格的集合，类似于表格。在工作表中，数据单元格由两个值标识:行号和列号。

![A spreadsheet](img/a7c7cc6bc71211718c6ca3d6b37097ab.png)

例如，在上面的屏幕截图中，电子表格只包含一个工作表“Sheet1”。单元格“2A”对应于第二行第一列。单元 2A 的值为 1。

尽管带有 <abbr title="graphical user interface">GUI</abbr> 的程序会给列名分配字母，但是当我们解析数据时，我们将从 0 开始行号和列号。这意味着，像元 2A 对应于(1，0)，4B 对应于(1，3)，3C 对应于(2，2)，依此类推。

## 设置 Python 环境

我们将使用 [Python 3](https://www.python.org/download/releases/3.0/) 来读写电子表格。要读写 XLSX 文件，您需要安装 Pandas 模块。您可以通过 Python 安装程序之一:`pip`或`easy_install`来完成。熊猫使用 [`openpyxl`](https://pypi.org/project/openpyxl/) 模块读取新的电子表格(。xlsx)文件，以及 [`xlrd`](https://pypi.org/project/xlrd/) 模块来读取遗留电子表格(。xls 文件)。当你安装熊猫时，这两个`openpyxl`和`xlrd`都作为依赖项被安装:

```
pip3 install pandas 
```

要读写 CSV 文件，您需要预装在 Python 中的`csv`模块。也可以通过熊猫读取 CSV 文件。

## 阅读电子表格

如果您有一个文件，并且想要解析其中的数据，您需要按此顺序执行以下操作:

1.  导入`pandas`模块
2.  打开电子表格文件(或工作簿)
3.  选择一个工作表
4.  提取特定数据单元格的值

### 打开电子表格文件

我们先用 Python 打开一个文件。为了跟进，您可以使用下面的[示例电子表格](https://uploads.sitepoint.com/wp-content/uploads/2021/06/1623754996sample-xlsx-file-for-testing.xlsx)，由[学习容器](https://www.learningcontainer.com/)提供:

```
import pandas as pd
workbook = pd.read_excel('sample-xlsx-file-for-testing.xlsx')
workbook.head() 
```

|  | 段 | 国家 | 产品 | 折扣带 | 售出单位 | 制造价格 | 廉价 | 总销售额 | 折扣 | 销售 | 齿轮 | 利润 | 日期 | 月份号 | 月份名称 | 年 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | 政府 | 加拿大 | 卡雷拉 | 没有人 | One thousand six hundred and eighteen point five | three | Twenty | Thirty-two thousand three hundred and seventy | Zero | Thirty-two thousand three hundred and seventy | Sixteen thousand one hundred and eighty-five | Sixteen thousand one hundred and eighty-five | 2014-01-01 | one | 一月 | Two thousand and fourteen |
| one | 政府 | 德国 | 卡雷拉 | 没有人 | One thousand three hundred and twenty-one | three | Twenty | Twenty-six thousand four hundred and twenty | Zero | Twenty-six thousand four hundred and twenty | Thirteen thousand two hundred and ten | Thirteen thousand two hundred and ten | 2014-01-01 | one | 一月 | Two thousand and fourteen |
| Two | 中端市场 | 法国 | 卡雷拉 | 没有人 | Two thousand one hundred and seventy-eight | three | Fifteen | Thirty-two thousand six hundred and seventy | Zero | Thirty-two thousand six hundred and seventy | Twenty-one thousand seven hundred and eighty | Ten thousand eight hundred and ninety | 2014-06-01 | six | 六月 | Two thousand and fourteen |
| three | 中端市场 | 德国 | 卡雷拉 | 没有人 | Eight hundred and eighty-eight | three | Fifteen | Thirteen thousand three hundred and twenty | Zero | Thirteen thousand three hundred and twenty | Eight thousand eight hundred and eighty | Four thousand four hundred and forty | 2014-06-01 | six | 六月 | Two thousand and fourteen |
| four | 中端市场 | 墨西哥 | 卡雷拉 | 没有人 | Two thousand four hundred and seventy | three | Fifteen | Thirty-seven thousand and fifty | Zero | Thirty-seven thousand and fifty | Twenty-four thousand seven hundred | Twelve thousand three hundred and fifty | 2014-06-01 | six | 六月 | Two thousand and fourteen |

Pandas 将电子表格作为表格读取，并将其存储为一个 [Pandas dataframe](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) 。

如果文件包含非 ASCII 字符，则应以 unicode 格式打开，如下所示:

```
import sys
workbook = pd.read_excel('sample-xlsx-file-for-testing.xlsx', encoding=sys.getfilesystemencoding()) 
```

如果您的电子表格非常大，您可以添加一个参数`use_cols`，它只将某些列加载到数据帧中。例如，以下参数将只读取前五列:

```
workbook = pd.read_excel('~/Desktop/import-export-data.xlsx', usecols = 'A:E')
workbook.head() 
```

|  | 段 | 国家 | 产品 | 折扣带 | 售出单位 |
| --- | --- | --- | --- | --- | --- |
| Zero | 政府 | 加拿大 | 卡雷拉 | 没有人 | One thousand six hundred and eighteen point five |
| one | 政府 | 德国 | 卡雷拉 | 没有人 | One thousand three hundred and twenty-one |
| Two | 中端市场 | 法国 | 卡雷拉 | 没有人 | Two thousand one hundred and seventy-eight |
| three | 中端市场 | 德国 | 卡雷拉 | 没有人 | Eight hundred and eighty-eight |
| four | 中端市场 | 墨西哥 | 卡雷拉 | 没有人 | Two thousand four hundred and seventy |

此外，您可以使用`nrows`和`skiprows`参数分别只读取一定数量的行，或者忽略开头的一定数量的行。

### 打开特定的工作表

您可以使用`sheet_name`参数从电子表格中选择某个工作表。默认情况下， [read_excel()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_excel.html) 函数解析文件中的第一张工作表。您可以提供字符串形式的工作表名称，或者工作表的索引(从 0 开始):

```
# Read the sheet with the name 'Sheet1'
worksheet = pd.read_excel('sample-xlsx-file-for-testing.xlsx', sheet_name = 'Sheet1') 
```

```
# Read the 1st sheet in the file
worksheet = pd.read_excel('sample-xlsx-file-for-testing.xlsx', sheet_name = 0) 
```

您也可以通过向参数`sheet_name`传递一个列表来选择要存储为熊猫数据帧的 dict 的工作表数量:

```
# Read the first two sheets and a sheet with the name 'Sheet 3'
worksheets = pd.read_excel('~/Desktop/import-export-data.xlsx', sheet_name = [0, 1, 'Sheet 3']) 
```

### 从单元格获取数据

一旦将工作表选择到数据框架中，就可以通过查询 Pandas 数据框架来提取特定数据单元格的值:

```
import pandas as pd
workbook = pd.read_excel('sample-xlsx-file-for-testing.xlsx')

# Print the 1st value of the Product column
print(workbook['Product'].iloc[0])

=> Carretera 
```

[。iloc()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.iloc.html) 方法帮助你搜索一个基于索引位置的值。在上面的代码中，`.iloc()`搜索第 0 个索引位置的值。类似地，您可以通过[使用标签来搜索值。loc()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.loc.html) 方法。例如，如果您将参数`0`传递给`.loc()`方法，它将在索引中搜索标签`0`:

```
print(workbook['Product'].loc[0])

=> Carretera 
```

一旦数据集被加载到数据框架中，您就可以使用 Pandas 中的内置函数对其进行查询。这里有一篇关于[探索你的熊猫数据框架](https://realpython.com/pandas-python-explore-dataset/)的文章。

## 创建电子表格

创建工作表的工作流程与上一节类似。

1.  导入`pandas`模块
2.  将数据保存到工作簿中
3.  在工作簿中创建工作表
4.  为工作簿中的单元格添加样式

### 创建新文件

要创建一个新文件，我们首先需要一个数据框架。让我们从文章的顶部重新创建演示表:

```
import pandas as pd

name = ['John', 'Mary', 'Sherlock']
age = [11, 12, 13]
df = pd.DataFrame({ 'Name': name, 'Age': age })
df.index.name = 'ID' 
```

然后，您可以通过调用 dataframe 上的 [to_excel()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_excel.html) 函数来创建一个新的电子表格文件，并指定它应该保存为的文件名:

```
df.to_excel('my_file.xlsx') 
```

您也可以使用功能`read_excel()`打开同一文件。

### 添加工作表

您可以使用`sheet_name`参数将数据框保存为工作簿中的某个工作表。该参数的默认值为`Sheet1`:

```
df.to_excel('my_file.xlsx', sheet_name = 'My Sheet') 
```

### 保存电子表格时的更多选项

您可以使用 [ExcelWriter](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.ExcelWriter.html#pandas.ExcelWriter) 类在保存到电子表格时获得更多选项。如果希望将多个数据帧保存到同一个文件中，可以使用以下语法:

```
import pandas as pd

workbook = pd.read_excel('my_file.xlsx')

# Creating a copy of workbook
workbook_2 = workbook.copy()

with pd.ExcelWriter('my_file_1.xlsx') as writer:
    workbook.to_excel(writer, sheet_name='Sheet1')
    workbook_2.to_excel(writer, sheet_name='Sheet2') 
```

要将数据帧追加到现有的电子表格中，请使用`mode`参数。请注意，只有当您将引擎指定为`openpyxl`时，才支持追加模式:

```
with pd.ExcelWriter('my_file_1.xlsx', engine="openpyxl", mode='a') as writer:
    workbook_2.to_excel(writer, sheet_name='Sheet3' 
```

此外，使用`date_format`和`datetime_format`设置日期和时间值:

```
with pd.ExcelWriter('my_file.xlsx',
  date_format='YYYY-MM-DD',
    datetime_format='YYYY-MM-DD HH:MM:SS') as writer:
  workbook.to_excel(writer) 
```

## 阅读遗产(。xls)电子表格

您可以在 Pandas 中使用相同的语法读取带有`.xls`扩展名的传统电子表格:

```
workbook = pd.read_excel('my_file_name.xls') 
```

当您使用相同的`read_excel()`函数时，Pandas 使用`xlrd`引擎来读取它。您可以使用我们在本教程前面讨论过的相同语法来读写遗留电子表格。

## CSV 文件的快速摘要

CSV 代表“逗号分隔值”(或者有时是字符分隔的，如果使用的分隔符是除逗号以外的某个字符)，其名称不言自明。典型的 CSV 文件如下所示:

```
'ID', 'Name', 'Age'
'1', 'John', '11'
'2', 'Mary', '12'
'3', 'Sherlock', '13' 
```

您可以将电子表格转换为 CSV 文件以简化解析。CSV 文件可以使用 Python 中的`csv`模块轻松解析，此外还有 Pandas:

```
workbook = pd.read_csv('my_file_name.csv') 
```

## 结论

正如我前面提到的，当您使用大型 web 应用程序时，创建和解析电子表格是不可避免的。因此，熟悉解析库只会在需要时对您有所帮助。

你用什么脚本语言处理电子表格？Python 有其他你喜欢的库吗？请随时在推特上给我打电话。

## 分享这篇文章