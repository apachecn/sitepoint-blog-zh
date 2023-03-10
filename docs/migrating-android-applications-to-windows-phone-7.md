# 将 Android 应用迁移到 Windows Phone 7

> 原文：<https://www.sitepoint.com/migrating-android-applications-to-windows-phone-7/>

本文向您介绍了一种将使用 Eclipse for Android 编写的 Java 应用程序移植到使用 Visual Studio、C#和 Silverlight 的 Windows Phone 7 的实用方法。您将看到一个完整的 Android 应用程序，并了解如何将其用户界面和代码迁移到 Windows Phone 7。您将看到如何使用微软的互用性桥来帮助 API 翻译。

### Android StckPckr 应用程序

该应用程序是一个有趣的小工具，它查看股票价格历史，并对股票进行技术分析，以确定现在是买入还是卖出的好时机。请注意，这只是一个演示，你不应该根据它来做出投资决定。

它会查看过去两个月的股价历史，并计算股票的“布林线”。这些为股票的统计相关值提供了一个指标。当股票处于波段底部时，表明这可能是买入股票的好机会，因为其价格相对较低。同样，如果它的价格相对较高，它将在该波段的顶端。

该应用程序将允许你输入股票行情，并计算波段。然后，它会将股票的当前价格转换为该区间内的位置，0 为底部，100 为顶部。它将使用这个来驱动 UI 中的滑块，向您显示价格的相对位置。

例如，图 1 显示了在 Android 2.3.3 虚拟设备上运行的 2011 年 11 月 14 日 AAPL 的计算结果。它的计算基于前一天的收盘价。对于苹果来说，这是 384.62 美元。今天，当我运行应用程序时，股票的交易价格是 379.77 美元，这表明该方法非常准确！

[![1_Android_StckPckr_App](img/d5b71c8a8b276ad26c7d51430b79a34f.png "1_Android_StckPckr_App")](https://www.sitepoint.com/wp-content/uploads/2011/12/1_Android_StckPckr_App.png)

1 个 Android StckPckr 应用程序

这个应用程序是一个很好的、简单的应用程序移植示例，因为它:

1.  在 Main.xml 中有一个 XML 定义的用户界面。
2.  使用代码初始化 UI 的某些部分，例如，将状态滑块默认为“50”，以及使用代码动态更新 UI，在分析完成后更改滑块的值。
3.  使用网络堆栈通过 internet 使用信息，并要求清单提供允许这样做的权限。
4.  使用纯 Java 进行计算数据的数学运算。

不管你的应用程序是什么或做什么，大多数应用程序都会有这些功能，所以，如果你能移植这个应用程序，你就能很好地移植任何应用程序！

### Windows Phone 入门

在这一点上，我假设您已经有了 Eclipse、Android SDKs 和 Android 开发所需的一切。如果你不知道，请访问 Android 开发者门户网站。稍后，我们将使用 StckPckr 应用程序来查看 Eclipse。

对于 Windows Phone 开发，你可以使用 Visual Studio Professional，如果你已经有了它，或者，如果没有，免费的 Windows Phone Visual Studio 2010 Express 可以很好地完成这项工作。您可以使用链接从 Microsoft 下载该软件。

如果您刚刚入门，Visual Studio 的免费版和专业版之间并没有太大的区别，所以如果您只想使用免费版，也不用担心。在使用 Visual Studio Express 阅读本文时，您可能会看到的主要区别是您可以使用的应用程序模板较少。

要开始，一旦你安装了 Visual Studio，启动它，并从文件菜单中，选择“新建项目”。您应该会看到模板类型的列表。在左侧选择“Silverlight for Windows Phone ”,并从可用类型列表中选择“Windows Phone 应用程序”。参见图 2。

[![2_Launch_Windows_Phone_App](img/22b111f5c524ebc6d17b883711b576b5.png "2_Launch_Windows_Phone_App")](https://www.sitepoint.com/wp-content/uploads/2011/12/2_Launch_Windows_Phone_App.png)

2 启动 Windows Phone 应用程序

将应用程序命名为“StckPckr7 ”,然后按“确定”。选择 Windows Phone 7.1 作为您的目标操作系统，然后按“确定”。

Visual Studio 将为您创建一个新的 Windows Phone 7 项目，并将用户界面加载到设计器中。它将为您提供 UI 的可视化表示和定义它的 XML 标记。微软称 UI XML 标记为“XAML”，代表 XML 应用程序标记语言。参见图 3。

￼

[![3_Visual_Studio_Designing_UI](img/0008ea6904125a2707715d33560b7f7c.png "3_Visual_Studio_Designing_UI")](https://www.sitepoint.com/wp-content/uploads/2011/12/3_Visual_Studio_Designing_UI.png)

3 Visual Studio 设计用户界面

作为使用 Eclipse 的 Android 开发人员，您将习惯于根据不同的操作系统或目标设备类型配置多个 AVM，从 Android 2.1 手机一直到 Android 4.0 平板电脑。由于 Windows phones 中的差异相对较小，仿真体验不需要这样的配置。只需按下 Run 按钮(或 F5)即可在模拟器中启动您的应用程序，如图 4 所示。

￼

[![4_Windows_Phone_Emulator](img/19264808b75fa8a76854e387e2d8f974.png "4_Windows_Phone_Emulator")](https://www.sitepoint.com/wp-content/uploads/2011/12/4_Windows_Phone_Emulator.png)

4 Windows Phone 模拟器

太好了，现在您已经拥有了开始工作所需的一切。所以让我们切换回 Eclipse，看看 Android 应用程序。

### 将 Android 用户界面移植到 XAML

最好的起点是定义 StckPckr 应用程序 UI 的 XML，并将其迁移到 XAML。在 Eclipse 的 app/res/layout 文件夹中，您会看到一个名为“Main.xml”的文件。打开它，您可能会在可视化设计器中看到它，如图 5 所示。

￼

[![5_StckPckr_UI_Android_Eclipse](img/6db222feca92d524ad62e0d042a94ccb.png "5_StckPckr_UI_Android_Eclipse")](https://www.sitepoint.com/wp-content/uploads/2011/12/5_StckPckr_UI_Android_Eclipse.png)

5 StckPckr UI Android Eclipse

我发现图中右边的 Outline 视图特别有用。它显示了该 UI 中每个控件的类，正如您在本例中看到的，它是:

*   线性布局
    *   textView1“欢迎使用 StckPckr”
    *   线性布局 1
        *   文本视图 2–“输入滚动条”
        *   editText1
        *   按钮 1-“开始”
    *   SeekBar1
    *   textview 3-'–买入卖出-'

第一个 LinearLayout 是视图的根，它包含 4 个控件:显示“欢迎使用 StckPckr”的 TextView 一个 LinearLayout 控件，它本身包含许多控件；搜索栏；和另一个文本视图。这些控件垂直布局。

名为“linearLayout1”的 LinearLayout 是第一个的子元素，但它是水平方向的，并且包含一个 TextView、一个 EditText 字段和一个按钮。

这些控件中的每一个在 Windows Phone 7 中都有相应的控件，而找出它们是什么的一个很好的方法就是使用微软提供的 [API 映射工具](http://wp7mapping.interoperabilitybridges.com/library?source=Android)。
例如，使用 API 映射工具查找 LinearLayout 控件。它会将你映射到一个叫做“StackPanel”的东西，如果你查找它的水平和垂直枚举，你会看到它们对应于 Windows Phone 7 中一个叫做“Orientation”的枚举。同样，您可以看到以下映射:

*   按钮->按钮
*   EditText -> TextBox
*   搜索栏->滑块

API 映射器的当前实例中缺少 TextView，但在 Windows 中它被称为 TextBlock。因此，您的用户界面的粗略翻译应该如下所示:

*   堆栈面板(Orientation="Vertical ")
    *   TextBlock“欢迎使用 StckPckr7”
    *   堆栈面板(方向= "水平")
        *   TextBlock'输入 Ticker '
        *   文本框
        *   按钮-“开始”
    *   text block-'–买入卖出-'

稍微搜索一下，你也可以找到一些其他的好东西。例如，Android 中的文本对齐是使用“gravity”完成的，例如，您可以看到 TextView 控件是这样使用它的:

```
<TextView
    android:id="@+id/textView1"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:gravity="center_horizontal"
    android:text="Welcome to StckPckr"
    android:textAppearance="?android:attr/textAppearanceLarge" /> 
```

对 API 映射器的快速搜索显示，对于许多 Android 控件来说，重力映射为对齐。Visual Studio 中智能感知的巧妙之处不在于它是通过将您键入的字符与属性的第一部分相匹配来触发的，而是对属性的任何部分进行触发。例如，如果你正在编辑一个文本块，你输入“Align ”,你会看到“TextAlignment”显示为一个可用属性，这是你用来做文本居中的方式，就像重力在 Android 中做的那样。

### 编辑您的 XAML

Visual Studio 中的 XAML 编辑器非常友好，并提供智能感知和自动完成功能来帮助您填写标签。在 Visual Studio 中查看一下，您的`MainPage.xaml`将如下所示:

```
<phone:PhoneApplicationPage 
    x:Class="StckPckr7.MainPage"

    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:phone="clr-namespace:Microsoft.Phone.Controls;assembly=Microsoft.Phone"
    xmlns:shell="clr-namespace:Microsoft.Phone.Shell;assembly=Microsoft.Phone"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d" d:DesignWidth="480" d:DesignHeight="768"
    FontFamily="{StaticResource PhoneFontFamilyNormal}"
    FontSize="{StaticResource PhoneFontSizeNormal}"
    Foreground="{StaticResource PhoneForegroundBrush}"
    SupportedOrientations="Portrait" Orientation="Portrait"
    shell:SystemTray.IsVisible="True">

<!--LayoutRoot is the root grid where all page content is placed-->
<Grid Name="LayoutRoot" Background="Transparent">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>

    <!--TitlePanel contains the name of the application and page title-->
    <StackPanel Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
        <TextBlock Name="ApplicationTitle" Text="MY APPLICATION" Style="{StaticResource PhoneTextNormalStyle}"/>
        <TextBlock Name="PageTitle" Text="page name" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
    </StackPanel>

    <!--ContentPanel - place additional content here-->
    <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0"></Grid>
</Grid>

<!--Sample code showing usage of ApplicationBar-->
<!--<phone:PhoneApplicationPage.ApplicationBar>
    <shell:ApplicationBar IsVisible="True" IsMenuEnabled="True">
        <shell:ApplicationBarIconButton IconUriimg/appbar_button1.png" Text="Button 1"/>
        <shell:ApplicationBarIconButton IconUriimg/appbar_button2.png" Text="Button 2"/>
        <shell:ApplicationBar.MenuItems>
            <shell:ApplicationBarMenuItem Text="MenuItem 1"/>
            <shell:ApplicationBarMenuItem Text="MenuItem 2"/>
        </shell:ApplicationBar.MenuItems>
    </shell:ApplicationBar>
</phone:PhoneApplicationPage.ApplicationBar>-->

</phone:PhoneApplicationPage> 
```

突出显示的部分是您将要绘制 UI 的地方。剩下的就是操作系统为你提供的用户界面了，但是你应该把指定“我的应用程序”和“页面名称”的文本块控件编辑得更友好一些。

现在，在您的内容区域中，名为 ContentPanel 的网格，您可以按照上面的方式定义 UI。当您键入标记时，intellisense 会帮助您，使您很容易找到正确的属性。它应该是这样的:

```
<Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
    <StackPanel Orientation="Vertical">
        <TextBlock Text="Welcome to StckPckr7 for Windows Phone"></TextBlock>
        <StackPanel Orientation="Horizontal" Margin="0,20,0,0">
            <TextBlock Text="Enter a Ticker"></TextBlock>
            <TextBox Text="" Width="200"></TextBox>
            <Button Content="Go"></Button>
        </StackPanel>
        <Slider Maximum="100" Value="50" Margin="0,20,0,0"></Slider>
        <TextBlock Text=" -- Buy    Sell --" TextAlignment="Center"></TextBlock>
    </StackPanel>
</Grid> 
```

这会给你一个与 Android 非常相似的用户界面。按 F5 在模拟器中启动它。您将看到类似于图 6 的内容。

￼

[![6_Running_Ported_UI](img/dffebed87db7ae914a6d8269c93aaa23.png "6_Running_Ported_UI")](https://www.sitepoint.com/wp-content/uploads/2011/12/6_Running_Ported_UI.png)

6 运行移植的 UI

显然这不是一个非常漂亮的 UI，但是它展示了如何开始移植。通过调整属性，或者使用 Expression Blend 作为一个所见即所得的工具，你可以使用户界面更好。但是为了简洁起见，在本文的其余部分，我们将让它保持这种视觉效果。

在继续编写代码之前，您还需要在 XAML 中编辑一件事情，那就是为每个控件命名。在 Android 中，你在 XML 声明中使用`android:id`。在 Windows Phone 7 中，你使用`Name`，像这样:

```
<StackPanel Orientation="Vertical">
    <TextBlock Text="Welcome to StckPckr7 for Windows Phone"></TextBlock>
    <StackPanel Orientation="Horizontal" Margin="0,20,0,0">
        <TextBlock Text="Enter a Ticker"></TextBlock>
        <TextBox Name="TextBox1" Text="" Width="200"></TextBox>
        <Button Name="Btn1" Content="Go"></Button>
    </StackPanel>
    <Slider Name="Slider1" Maximum="100" Value="50" Margin="0,20,0,0"></Slider>
    <TextBlock Text=" -- Buy    Sell --" TextAlignment="Center"></TextBlock>
</StackPanel> 
```

您不需要命名所有的控件，只需命名您想要在代码中处理的控件即可。您将在下一节看到如何做到这一点。

### 移植代码

**第一步。理解视图定义和视图代码是如何连接的**

在 Android 中，您通常会将代码编写为支持视图的活动。当您编译时，会生成 R.java，并且驻留在活动中的视图代码必须使用如下代码从 R.java 加载视图的 UI:

```
setContentView(R.layout.main); 
```

其中，在这种情况下，使用`Main.xml`定义视图。

Windows Phone 7 视图的代码与其视图定义的绑定更加紧密。通常，如果您有一个名为`MainPage.xaml`的视图，您将在`MainPage.xaml.cs`中为它编写代码。如果您查看项目的解决方案资源管理器，您会看到这一点。参见图 7。

[![7_Connecting_XAML_Code_CS](img/9384c6c8f80dbe73d79915d33acdf22f.png "7_Connecting_XAML_Code_CS")](https://www.sitepoint.com/wp-content/uploads/2011/12/7_Connecting_XAML_Code_CS.png)

7 连接 XAML 电码 CS

这是一个微软开发人员称之为“代码隐藏”的过程，其中页面的代码在页面的“后面”。您可以将代码文件命名为您喜欢的任何名称，只要它编译成的类能够被页面识别。如果您看一下 XAML 页面的顶部，您会看到它使用`x:Class`节点定义了它的代码类。所以，在这种情况下你会看到:

```
x:Class="StckPckr7.MainPage" 
```

这意味着`MainPage`类包含了这个视图的代码。看一看`MainPage.xaml.cs`，您会看到它定义了`MainPage`类:

```
public partial class MainPage : PhoneApplicationPage
{
} 
```

所以现在您可以在这个类中编写代码，它将处理在`MainPage.xaml`中定义的 UI。

**第二步。处理应用程序或查看生命周期事件**

在 Android 应用程序中，通常会在`onCreate`函数中对 UI 进行任何基于代码的初始化。这个函数在应用程序创建视图时被调用，但是在它呈现视图之前。因此，您希望将初始化 UI 组件的代码放入其中(例如设置 Seekbar 最大值和当前点)，以便它们在绘制之前准备好。这是我们应用程序中 onCreate 的完整代码。

```
public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  setContentView(R.layout.main);
  final EditText edt = (EditText)findViewById(R.id.editText1);
  seek = (SeekBar)findViewById(R.id.seekBar1);
  seek.setMax(100);
  seek.setProgress(50);
  Button btn = (Button)findViewById(R.id.button1);  

  btn.setOnClickListener(new OnClickListener() 
    {            
        public void onClick(View v) 
        {                
            String strTicker = edt.getText().toString();   
            try{
                analyzeTicker(strTicker);
            }
            catch(Exception ex)
            {
                // Do nothing
            }
        }       
    });
} 
```

如果你使用 API 映射工具查找 [Activity.onCreateView](http://wp7mapping.interoperabilitybridges.com/Library.aspx?filterpath=Android-Gingerbread_v2_3-android_app-Activity-Method-onCreateView&source=Android) ，你会看到 Windows Phone 7 中的对应项叫做 Application_Launching。这是事实，但不完全正确。

Application_Launching 在应用程序加载并呈现其 UI 之前触发。但是如果你的 UI 有一个支持它的类，管理这个类的初始化的更好的方法是在这个类的构造函数中完成。这样，UI 对象可以是“局部”变量，而不是“全局”变量，如果你从一个全局类寻址它们，它们就必须是“全局”变量。这是更好的面向对象编程方式。作为参考，您应该探索应用程序生命周期事件。它们是在 Visual Studio 的`App.xaml`和`App.xaml.cs`中为您准备的。

但是对于这个应用程序来说，我们对 UI 所做的任何初始化都应该在`MainPage()`构造函数中完成。在我们的 Android 应用程序中，代码做了三件事:

*   获取对 EditText 的引用，并将其命名为 edt，这样我们就可以在代码中引用它
*   获取对 SeekBar 的引用，并将其命名为 seek，这样我们就可以在代码中引用它
*   获取按钮的句柄，将其命名为 btn，并添加一个 OnClickListener 事件处理程序

在我们的 Windows Phone 7 中，这个过程稍微简单一些

*   我们不需要获取对文本框的引用，在代码中引用它。通过在 XAML 中使用 Name 对其进行命名，我们已经可以使用名称 TextBox1 对其进行访问
*   类似地，我们可以使用 XAML 定义的名称来引用滑块(对应于 SeekBar)
*   类似地，我们可以直接通过按钮的名称来引用它，但是我们仍然需要为它添加一个事件处理程序。

如果您以前从未使用过 Visual Studio，请仔细按照下面的步骤来查看更酷的自动完成功能之一。打开`MainPage.xaml.cs`并将光标放在构造函数中。

输入“bt”，你会看到一个菜单，里面有 Btn1。它认识到你有这个对象。按 TAB 键，它会自动为您填写。

键入一个点“.”，然后是字母‘C’、‘L’和‘I’。你会看到菜单跟随你的输入，现在“点击”将在列表的顶部。再次点击 TAB 键进行填写。

现在真正酷的部分来了。键入“+=”，会出现一个灰色小框，提示您需要一个新的路由事件处理程序。按 TAB 键，代码将被插入。再次按 TAB，将为您生成并插入事件处理程序的完整存根！这比记住事件处理函数语法和返回类型要容易得多。您的代码现在应该是这样的:

```
public MainPage()
{
    InitializeComponent();
    Btn1.Click += new RoutedEventHandler(Btn1_Click);
}

void Btn1_Click(object sender, RoutedEventArgs e)
{
    throw new NotImplementedException();
} 
```

这比你在 Android 活动中编写的代码要少得多。现在让我们实现按钮的 Click 事件处理程序。首先，我们再来看看 Android 版本的 Java 代码:

```
btn.setOnClickListener(new OnClickListener() 
    {            
        public void onClick(View v) 
        {                
            String strTicker = edt.getText().toString();   
            try{
                analyzeTicker(strTicker);
            }
            catch(Exception ex)
            {
                // Do nothing
            }
        }       
    }); 
```

每当用户按下按钮时，事件处理程序读取 EditText 的内容，从中创建一个字符串，并将其传递给 analyze ticker。在 C#中这样做是非常相似的。将 Btn1_Click 更新为以下内容:

```
void Btn1_Click(object sender, RoutedEventArgs e)
{
  String strTicker = TextBox1.Text;
  analyzeText(strTicker);
} 
```

你可以像在 Java 中一样在 C#中使用`try..catch()`。我在这里省略了它，因为它在 C#版本的`analyzeText`函数中是不必要的，您将在下面看到。

**第三步。处理纯 Java 代码**

至此，您已经看到了如何首先将您的 UI(用 XML 定义)迁移到基于 XAML 的 UI。然后，您看到了 XAML 和 C#如何合作，以类似于 Java 活动的方式，为视图提供代码隐藏。然后您看到了一些应用程序生命周期，如何在启动视图时运行代码。

最后，您了解了如何在代码中引用 UI 控件，以及如何“连接”事件处理程序，以便您可以编写响应应用程序中事件的代码。最后，我们将看看 Java 应用程序的核心，即获取用户输入、从 web 上检索数据、分析数据并使用该分析向用户提供反馈的代码。

**3.1。创建雅虎财经 URI**

这个应用程序的 Java 代码可以分为 3 个步骤。首先是为数据服务创建一个 URI。在本例中，我们使用的是 Yahoo financial 数据，该数据是通过 HTTP 调用以 CSV(逗号分隔值)形式检索的。这里有一个很好的语法教程:[http://www.gummy-stuff.org/Yahoo-data.htm](http://www.gummy-stuff.org/Yahoo-data.htm)

下面是实现这一点的 Java 代码:

```
// Create the Yahoo URI  
Calendar today = Calendar.getInstance();
int today_day = today.get(Calendar.DATE);
int today_month = today.get(Calendar.MONTH) + 1;
int today_year = today.get(Calendar.YEAR);
Calendar start = Calendar.getInstance();
start.add(Calendar.MONTH, -2);
int start_day = start.get(Calendar.DATE);
int start_month = start.get(Calendar.MONTH) + 1;
int start_year = start.get(Calendar.YEAR);

StringBuilder uri = new StringBuilder();
uri.append("http://ichart.finance.yahoo.com/table.csv");
uri.append("?s=").append(strTicker);
uri.append("&a=").append(start_month);
uri.append("&b=").append(start_day);
uri.append("&c=").append(start_year);
uri.append("&d=").append(today_month);
uri.append("&e=").append(today_day);
uri.append("&f=").append(today_year);
uri.append("&g=d"); 
```

它查找今天的日期，并将其转换为日、月和年的值，然后是两个月前的日期，做同样的事情，并将这些值插入到构建 URI 的 StringBuilder 中。

虽然 Windows Phone 7 有一个“日历”对象，但它实际上是一个代表日历的 UI 控件。所以不要被这个搞糊涂了！在 C#中，相当于 Java 日历的是 DateTime 类。

**3.2。创建一个 HTTP 请求来获取数据，并读取响应**

下一步是 Java Android 代码创建一个 HttpClient 对象来管理到服务的连接，一个 HttpGet 对象来表示将对该服务采取的操作(HTTP 请求通常是 POST 或 Get，在本例中是 GET)，一个 HttpResponse 对象来在客户机上执行命令并读取响应。

```
HttpClient client = new DefaultHttpClient();
HttpGet request = new HttpGet();
request.setURI(new URI(uri.toString()));
HttpResponse response = client.execute(request); 
```

使用 API mapper，我发现 WebClient 类可能与我想为 HttpClient 做的事情非常匹配。WebClient 也有一个异步调用来读取 URL 的内容，所以我使用了以下代码:

```
WebClient client = new WebClient();
client.OpenReadCompleted += new OpenReadCompletedEventHandler(client_OpenReadCompleted);
client.OpenReadAsync(new Uri(uri.ToString())); 
```

在输入第二行时，使用 TAB 键提供的代码插入，我创建了一个事件处理函数。因此，在这种情况下，一旦手机从 URI 读取数据，它将回调该函数，从而不会锁定任何线程。

此时，在 Java 中，您将使用 BufferedReader 逐行读取数据。每一行都是 CSV 格式的，所以当它作为一个字符串被读取时，你可以用逗号把它分开。我们想要的数据项(收盘价)是第五项，所以一旦我们分割脚本，它就被添加到一个数组列表中。代码如下:

```
in = new BufferedReader(new InputStreamReader(response.getEntity().getContent()));
String line = "";
while ((line = in.readLine()) != null) 
{
String[] rowData = line.split(",");
Double dummy = new Double(rowData[4]);
    nTotal+=dummy;
    closePrices.add(dummy);
} 
```

在 C#中，我们做完全相同的事情。需要做一些修改，因为阅读器在 Java 中被称为“in”。这是 C#里的保留字，不能用。除此之外，代码非常相似，只是 C#不支持动态数组的 ArrayList，所以使用了`List<Double>`:

```
void client_OpenReadCompleted(object sender, OpenReadCompletedEventArgs e)
  {
    System.IO.StreamReader instream = new System.IO.StreamReader(e.Result);
    instream.ReadLine(); //Read the first line
    String line;
    List<Double> closePrices = new List<Double>();
    while ((line=instream.ReadLine())!=null)
    {
      string[] rowData = line.Split(',');
      Double dummy = new Double();
      dummy = Convert.ToDouble(rowData[4]);
      closePrices.Add(dummy);
    }
        instream.Close();
  } 
```

**3.3 数据处理**

最后一步是分析数据，通过计算布林线，找出它们的包络，然后找出当前价格在这个包络中的位置，以百分比的形式，底部是 0%，顶部是 100%。

解释这背后的统计分析超出了本文的范围，但简单地说，计算范围内的平均值，然后从中计算标准差。顶部是平均值+ 2 个标准差，底部是平均值-2 个标准差。

这是 Java 和 C#非常相似的地方，你会从代码中看到只需要很小的调整——主要是因为 C#程序使用了一个`List<Double>`而不是数组列表，所以它的 API 有点不同，并且因为它不需要强制转换来获得变量的输入或输出。

你会看到数学库几乎是相同的，Java 'Math.pow '有' Math。C#中的“Pow”等效，类似于“Math.sqrt”和“Math”。Sqrt。下面是 Java 代码:

```
Double nAverage = nTotal/closePrices.size();
Double sum=0.0;
for (int i=0; i<closePrices.size(); i++)
{
    sum+= Math.pow((Double) closePrices.get(i) - nAverage, 2);
}
Double nDeviation = Math.sqrt(sum/(closePrices.size()-1));
Double bollingerTop = nAverage + (2*nDeviation);
Double bollingerBottom = nAverage - (2*nDeviation);
int nToday = closePrices.size()-1;
Double bCloseToday = (Double) closePrices.get(nToday);
Double range = bollingerTop - bollingerBottom;
Double value = bCloseToday - bollingerBottom;
Double score = 100*value/range;
seek.setProgress(score.intValue()); 
```

这是 C#:

```
Double nAverage = nTotal / closePrices.Count;
Double sum = 0.0;
for (int i = 0; i < closePrices.Count; i++)
{
    sum += Math.Pow(closePrices[i] - nAverage, 2);
}
Double nDeviation = Math.Sqrt(sum / (closePrices.Count - 1));
Double bollingerTop = nAverage + (2 * nDeviation);
Double bollingerBottom = nAverage - (2 * nDeviation);
int nToday = closePrices.Count - 1;
Double bCloseToday = closePrices[nToday];
Double range = bollingerTop - bollingerBottom;
Double value = bCloseToday - bollingerBottom;
Double score = 100 * value / range;
Slider1.Value = score; 
```

所以现在，如果我运行 Windows Phone 7 版本的应用程序，像我在 Android 上一样输入 AAPL，我会得到相同的结果。参见图 8。

[![8_Ported_Application](img/097e7a91aa35642a6f57da4cd61d5f7d.png "8_Ported_Application")](https://www.sitepoint.com/wp-content/uploads/2011/12/8_Ported_Application.png)

8 移植的应用程序

### 摘要

在本文中，您看到了如何使用 Visual Studio 将 Android 应用程序从 Eclipse 中的 Java 移植到 Windows Phone 7。您了解了[Windows Phone inter operability Bridges](http://windowsphone.interoperabilitybridges.com)网站，并了解了如何使用它来理解 Java 控件和对象的 Windows 范例。您看到了如何将 XML UI 迁移到 XAML，然后探索了应用程序生命周期和事件连接在 Visual Studio 中是如何工作的。最后，您编写了处理用户输入和输出的代码，并了解了该应用程序如何在 Windows Phone 7 上运行。

## 分享这篇文章