# 用 CarlosAg 的方式制作 Excel

> 原文：<https://www.sitepoint.com/making-excel-the-carlosag-way/>

我们在[Sitepoint ASP.NET 论坛](https://www.sitepoint.com/forums/forumdisplay.php?f=141)上经常看到的一个问题是“嘿，我的老板需要我用这个数据集做一个 Excel 电子表格。请帮帮忙。你是我被解雇前最后的希望了！”

现在，有许多方法可以生成 excel 电子表格。最明显的方法是使用 COM interop 和 Excel 自己的对象模型来完成这项工作。这可能是最糟糕的计划。首先，它需要在 web 服务器上安装一个 Excel 才能工作。第二，Excel 被设计成在用户模式下运行，并且会弹出修改的对话框——有效地冻结你的应用程序。我将忽略与 COM interop 相关的“有趣”任务和处理 Excel 对象模型的纯粹乐趣。别管可伸缩性等问题。

第二组显而易见的选项可能有点贵——各种商业 excel 生成库。这方面的另一个选择—可能是最有用的—是 Sql Server 2005 Reporting Services。但是，只有在您已经将它用于其他报告任务的情况下，单独为某些 excel 导出实现它才有点浪费。另一个老的备用方法是生成一个 html 表格，并告诉浏览器“是的，天哪，这是 excel。”但这充其量是一个有限的解决方案。开发商该怎么做？

**Enter CarlosAg**

但是还有一个选择。Excel 的现代版本[2002/XP 或更高版本]支持[Xml spre sheet 规范](http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dnexcl2k2/html/odc_xmlss.asp)。如果。NET 只擅长一件事，那就是 Xml。进入 [CarlosAg 的](http://blogs.msdn.com/carlosag/archive/2005/08/27/WhyIWroteExcelXmlWriter.aspx) [ExcelXmlWriter](http://www.carlosag.net/Tools/ExcelXmlWriter/Default.aspx) 库。我已经在一些项目中使用过它，我必须说它非常光滑。这是我见过的唯一一个免费的库，它允许开发者创建一个几乎全功能的电子表格——包括公式、大量样式的单元格和数据透视表——而不需要通过 COM 桥或支付大量现金。

现在，它不是没有问题。最重要的一点是，开发人员必须非常小心地正确格式化数据，因为在库级别没有类型过滤。就像在任何导出操作中一样，必须仔细测试输出，以确保它是可解析的。也没有本地人。NET 2.0 二进制文件。但是我还没有遇到一个让节目停止的错误。这绝对是一个可靠的库，而且显然非常便宜。

现在我们进入精彩部分——代码示例。比方说，为了子孙后代，您需要将一个数据集转储到 Carlos 的 writer 中。

首先，您需要一个类来整体处理数据集:

```
 using System.Data;
using CarlosAg.ExcelXmlWriter;

namespace WWB.ExcelDatasetWriter
{
	/// <summary>
	/// Creates an excel-friendly Xml Document from a dataset using the CarlosAg.ExcelXmlWriter library.
	/// </summary>

	public class ExcelDatasetWriter
	{
		public ExcelDatasetWriter()
		{}

		public Workbook CreateWorkbook(DataSet data)
		{
			//ensure valid data
			if (data==null)
			{
				throw new ArgumentNullException("data", "Data cannot be null.");
			}
			ensureTables(data);

			//Variable declarations
			//our workbook
			Workbook wb=new Workbook(); 
			//Our worksheet container
			Worksheet ws; 
			//Our DataTableWriter
			ExcelDataTableWriter edtw=new ExcelDataTableWriter(); 
			//Our sheet name
			string wsName; 
			//Our counter
			int tCnt=0; 

			//Loop through datatables and create worksheets
			foreach (DataTable dt in data.Tables)
			{
				//set the name of the worksheet
				if (dt.TableName!=null && dt.TableName.Length>0 && dt.TableName!="Table")
				{
					wsName=dt.TableName;
				}
				else
				{
					//Go to generic Sheet1 . . . SheetN
					wsName="Sheet" + (tCnt+1).ToString();
				}
				//Instantiate the worksheet
				ws=wb.Worksheets.Add(wsName);
				//Populate the worksheet
				edtw.PopulateWorksheet(dt, ws);
				tCnt++;
			}
			return wb;
		}

		private void ensureTables(DataSet data)
		{
			if (data.Tables.Count==0)
			{
				throw new ArgumentOutOfRangeException("data", "DataSet does not contain any tables.");
			}
		}
	}
} 
```

然后，您需要一个类来处理每个数据表

```
 using System;
using System.Data;
using CarlosAg.ExcelXmlWriter;

namespace WWB.ExcelDatasetWriter
{
	/// <summary>
	/// Writes a datatable to a worksheet using the CarlosAG.ExcelXmlWriter library.
	/// </summary>

	public class ExcelDataTableWriter
	{
		public ExcelDataTableWriter()
		{}

		public void PopulateWorksheet(DataTable dt, Worksheet toPopulate)
		{
			PopulateWorksheet(dt, toPopulate, true);
		}

		public void PopulateWorksheet(DataTable dt, Worksheet toPopulate, bool makeHeader)
		{
			//check valid input
			if (toPopulate==null)
			{
				throw new ArgumentNullException("toPopulate", "Worksheet cannot be null.");
			}
			if (dt==null)
			{
				throw new ArgumentNullException("dt", "DataTable cannot be null");
			}
			//Parse the columns
			ColumnType[] colDesc=parseColumns(dt);
			//Create header row
			if (makeHeader)
			{
				toPopulate.Table.Rows.Insert(0, makeHeaderRow(colDesc));
			}
			//Create rows
			foreach (DataRow row in dt.Rows)
			{
				toPopulate.Table.Rows.Add(makeDataRow(colDesc, row));
			}
		}

		#region row + cell making

		private WorksheetRow makeHeaderRow(ColumnType[] cols)
		{
			WorksheetRow ret=new WorksheetRow();
			foreach(ColumnType ctd in cols)
			{
				ret.Cells.Add(ctd.GetHeaderCell());
			}
			return ret;
		}

		private WorksheetRow makeDataRow(ColumnType[] ctds, DataRow row)
		{
			WorksheetRow ret=new WorksheetRow();
			WorksheetCell tmp=null;
			for (int i=0; i<row.Table.Columns.Count; i++)
			{
				tmp=ctds[i].GetDataCell(row[i]);
				ret.Cells.Add(tmp);
			}
			return ret;
		}

		#endregion

		#region column parsing
		private ColumnType[] parseColumns(DataTable dt)
		{
			ColumnType[] ret=new ColumnType[dt.Columns.Count];
			ColumnType ctd=null;
			for (int i=0; i<dt.Columns.Count; i++)
			{
				ctd=new ColumnType();
				ctd.Name=dt.Columns[i].ColumnName;
				getDataType(dt.Columns[i], ctd);
				ret[i]=ctd;
			}
			return ret;
		}

		private void getDataType(DataColumn col, ColumnType desc)
		{
			if (col.DataType==typeof(DateTime))
			{
				desc.ExcelType=DataType.DateTime;
			}
			else if (col.DataType==typeof(string))
			{
				desc.ExcelType=DataType.String;
			}
			else if (col.DataType==typeof(sbyte) 
				|| col.DataType==typeof(byte) 
				|| col.DataType==typeof(short) 
				|| col.DataType==typeof(ushort)
				|| col.DataType==typeof(int)
				|| col.DataType==typeof(uint)
				|| col.DataType==typeof(long)
				|| col.DataType==typeof(ulong)
				|| col.DataType==typeof(float)
				|| col.DataType==typeof(double)
				|| col.DataType==typeof(decimal)
				)
			{
				desc.ExcelType=DataType.Number;
			}
			else
			{
				desc.ExcelType=DataType.String;
			}
		}
		#endregion
	}
} 

```

最后，这个类依赖于一个类来处理单独的列类型。

```
 using System;
using CarlosAg.ExcelXmlWriter;

namespace WWB.ExcelDatasetWriter
{
	/// <summary>
	/// Creates a Column for CarlosAg.ExcelXmlWriter
	/// </summary>

	internal class ColumnType
	{
		public ColumnType()
		{}
		private string name;
		public string Name
		{
			get {return name;}
			set {name=value;}
		}

		private DataType excelType;
		public DataType ExcelType
		{
			get {return excelType;}
			set {excelType=value;}
		}

		public WorksheetCell GetHeaderCell()
		{
			WorksheetCell head=new WorksheetCell(Name, DataType.String);
			return head;
		}

		private string getDataTypeFormatString()
		{
			if (ExcelType==DataType.DateTime)
			{
				return "s";
			}
			return null;
		}

		public WorksheetCell GetDataCell(object data)
		{
			WorksheetCell dc=new WorksheetCell();
			dc.Data.Type=ExcelType;
			if (ExcelType==DataType.DateTime && data is DateTime)
			{
				DateTime dt=(DateTime)data;
				dc.Data.Text=dt.ToString("s");
			}
			else
			{
				string dataString=data.ToString();
				if (dataString == null || dataString.Length==0)
				{
					dc.Data.Type=DataType.String;
					dc.Data.Text=string.Empty;
				}
				else
				{
					dc.Data.Text=dataString;
				}
			}
			return dc;
		}
	}
} 

```

现在，前两个代码示例非常简单。但最后一个涉及图书馆的一些怪癖。主要问题是工作表单元格。Data.Text 属性实际上是一个字符串，不是一个对象。它直接把它的值写入电子表格。有时这可能与 DataType 属性冲突，导致有趣的(并且很难查明的)excel 错误。在任何情况下，GetCellData(object)方法对于一般用途的包装都相当有效。

现在，要让 excel 打开电子表格，您只需记住用适当的内容类型将其流回浏览器:

```
 ExcelDatasetWriter edw=new ExcelDatasetWriter();
	Workbook wb=CreateWorksheet(MyDataSet);
	Response.ContentType = "application/vnd.ms-excel";
	wb.Save(Response.OutputStream); 

```

所以，总结一下:

1.  写 excel 是普遍需要。
2.  在 web 服务器上使用 Excel 对象库不是一个好主意。
3.  使用托管代码编写 Excel 是一个好主意。
4.  CarlosAg 的 excel writer 帮助你不用踏入互操作领域就能生成 excel。

享受快乐编码。

## 分享这篇文章