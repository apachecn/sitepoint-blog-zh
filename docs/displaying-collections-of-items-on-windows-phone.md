# 在 Windows Phone 上显示项目集合

> 原文：<https://www.sitepoint.com/displaying-collections-of-items-on-windows-phone/>

移动应用程序经常需要向用户显示很长的项目列表。这些可以包含图像、文本或其他自定义组件。每个移动平台都有一套独特的组件和概念来显示这样的列表，Windows Phone 也不例外。在本教程中，我将向您展示如何制作您自己的定制项目列表，更改这些列表的顺序，显示选项，以及以编程方式添加项目。

你可以从 [GitHub](https://github.com/sitepoint-editors/Item-Collectors) 下载完整的项目。

## 入门指南

在 Visual Studio 中创建一个项目，并添加一个*主页*，通过向*添加两个按钮来在我们的`ListView`和`GridView`视图之间切换。xaml* 文件，它将把我们带到相应的页面。

```
<Button x:Name="listViewButton" Content="List View" Grid.Row="0"/>

<Button x:Name="gridViewButton" Content="Grid View" Grid.Row="1"/>
```

现在创建这两个页面，并将它们命名为 *GridViewPage* 和 *ListViewPage* 。接下来，我们需要创建页面和按钮之间的连接。

在*主页*的*设计视图*中，双击按钮会产生`onClick`事件，xaml 和 C#代码会自动添加。

按钮的代码应该如下所示。

```
<Button x:Name="listViewButton" Content="List View" Grid.Row="0" Click="listViewButton_Click" />

<Button x:Name="gridViewButton" Content="Grid View" Grid.Row="1" Click="gridViewButton_Click" />
```

如果你打开 *MainPage.xaml.cs* ，你会看到上面生成的两个方法。在这些里面我们需要调用*框架。导航*切换页面。

```
private void listViewButton_Click(object sender, RoutedEventArgs e)
{
    Frame.Navigate(typeof(ListViewPage));
}

private void gridViewButton_Click(object sender, RoutedEventArgs e)
{
    Frame.Navigate(typeof(GridViewPage));
}
```

主页面现在已经完成，看起来应该如下图所示，允许你在页面之间切换。

![MainPage](img/df92b090b47f2c565aaa30893d6bd822.png)

## 项目

在每个列表布局后面是构成列表中一个条目的项目。例如，在联系人列表中可能有联系人的姓名、电话号码、图片、电子邮件等。

对于我们的 *ListView* ，我将添加以下项目来创建一个联系人条目。

```
class ListViewItem
{
    public SolidColorBrush color { get; set; }
    public string firstName { get; set; }
    public string lastName { get; set; }
    public string mobileNumber { get; set; }
}
```

对于 *GridView* ，我们将创建一个 Windows store 的克隆。这是一个在网格中显示项目的完美例子。

```
class GridViewItem
{
    public SolidColorBrush color { get; set; }
    public string name { get; set; }
    public string price { get; set; }
    public string rate { get; set; }
}
```

我用颜色代替了图片，希望这足够清楚。确保在两个类中都添加了`using Windows.UI.Xaml.Media;`作为`SolidColorBrush`的依赖项。`get`和`set`可以被认为是<q>对每个参数的读/写权限</q>。

将 *ListViewItem.cs* 和 *GridViewItem.cs* 移动到一个新的 *Items* 文件夹中，使项目结构更加清晰。

## 模型

为了控制我们的项目列表，我们将使用模型，它是代码和设计之间的桥梁。如果我们想改变显示的项目，我们不应该改变*。xaml* 文件，而是更改我们模型中的项目，剩下的将在运行时处理。

创建 *ListViewModel.cs* 和 *GridViewModel.cs* 并将它们移动到一个名为 *Models* 的文件夹中。

为了创建一个条目列表，我们需要一个来自`System.Collections.ObjectModel`的`ObservableCollection`来保存条目。它和一个简单的列表一样，但是绑定了条目。这种类型的集合是动态改变元素的关键，因为它继承自`INotifyCollectionChanged`和`INotifyPropertyChanged`。

在 *ListViewModel.cs* 中添加:

```
public static ObservableCollection<ListViewItem> ListViewItems { get; set; }
private static Random random;
```

Random 是一种数据类型，用于为颜色、手机号码和速率生成随机值。在构造函数中，我们初始化了`ListViewItems`和`random`。

```
public ListViewModel()
{
    ListViewItems = new ObservableCollection<ListViewItem>();
    random = new Random();
}
```

要添加项目，我们需要一个方法来创建一个类型为`ListViewItem`的项目，并将其添加到集合中。

```
public static void AddItem()
{
    ListViewItems.Add(new ListViewItem() {
        firstName = "Contact",
        lastName = (ListViewItems.Count + 1).ToString(),
        mobileNumber = "+355-" + random.Next(10, 100) + "-" + random.Next(10,100) + "-" + random.Next(10,100) + "-" + +random.Next(100, 1000),
        color = new SolidColorBrush(new Color() {
            A = 255,
            R = (byte)random.Next(255),
            G = (byte)random.Next(255),
            B = (byte)random.Next(255),
        })
    });
}
```

出于演示目的，上面的值有意保持简单。你可以看到所有的联系人都有相同的名字，有一个数字`lastname`，一个随机的`mobileNumber`和颜色值。

**注意**:这个方法是静态的，因为当绑定容器的元素到一个`ListView`时，类的一个实例被创建，所以要添加元素到这个实例中，我们不需要创建一个新的对象。

同样，我们为`GridView`定义模型:

```
class GridViewModel
{
    public static ObservableCollection<GridViewItem> GridViewItems { get; set; }
    private static Random random;

    public GridViewModel()
    {
        GridViewItems = new ObservableCollection<GridViewItem>();
        random = new Random();
    }

    public static void AddItem()
    {
        GridViewItems.Add(new GridViewItem()
        {
            name = "APP NAME " + (GridViewItems.Count + 1),
            price = "free",
            rate = AddStars(random.Next(1,6)),
            color = new SolidColorBrush(new Color()
            {
                A = 255,
                R = (byte)random.Next(255),
                G = (byte)random.Next(255),
                B = (byte)random.Next(255),
            })
        });
    }

    private static string AddStars(int v)
    {
        string s = "";
        for (int i = 0; i < v; i++)
            s += "✰";

        return s;
    }
}
```

我们需要这个项目的最后一个模型(我将很快解释为什么)，它包括上面指定的两个模型。我将它命名为`ViewModel`，它包含一个类型为`ListViewModel`的对象和一个类型为`GridViewModel`的对象。

```
class ViewModel
{
    public ListViewModel listViewModel { get; set; }
    public GridViewModel gridViewModel { get; set; }

    public ViewModel()
    {
        listViewModel = new ListViewModel();
        gridViewModel = new GridViewModel();
    }
}
```

## 数据模板

我们希望确保我们的列表以一种吸引人的方式呈现，为此，我们需要为列表或网格中的数据指定一个通用模板。模板是包含项目的视图的特征，比如`ListView`和`GridView`。

将以下内容添加到相应的*中。xaml* 文件。

```
<ListView x:Name="listView" />
```

和

```
<GridView x:Name="gridView" />
```

向这些视图添加一个 *ItemTemplate* 的简单方法是从*设计视图*中。*在视图上右击*，选择*编辑附加模板- >编辑生成的模板(项目模板)- >创建空…* 。

![DataTemplate](img/cc957a2f6377c99fddb00a660752fadd.png)

将其命名为 *ListViewItemTemplate* 或 *GridViewDataTemplate* ，并确保它是在文档中定义的。

![DataTemplate](img/a2036775e15b12a0e527b675b6df59b9.png)

当你点击 *Ok* 时，`View`标签将会更新如下。

```
ItemTemplate="{StaticResource ListViewDataTemplate}"
```

您将在页面标记中看到以下代码。

```
</Page.Resources>
    <DataTemplate x:Key="ListViewItemTemplate">
        <Grid/>
    </DataTemplate>
</Page.Resources>
```

将`<Grid/>`替换为:

```
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto"/>
        <ColumnDefinition/>
    </Grid.ColumnDefinitions>

    <Rectangle Height="75" Width="75" Grid.RowSpan="2" Fill="{Binding color}" Margin="5"/>
    <StackPanel Orientation="Horizontal" Grid.Column="1" VerticalAlignment="Bottom">
        <TextBlock x:Name="firstNameTB" TextWrapping="Wrap" Margin="15,0" Style="{StaticResource ListViewItemTextBlockStyle}" Text="{Binding firstName}" FontSize="33.333"/>
        <TextBlock x:Name="lastNameTB" TextWrapping="Wrap" Text="{Binding lastName}" Style="{StaticResource ListViewItemTextBlockStyle}" FontSize="33.333"/>
    </StackPanel>
    <TextBlock x:Name="numberTB" TextWrapping="Wrap" Text="{Binding mobileNumber}" Grid.Row="1" Grid.Column="1" Style="{StaticResource ListViewItemSubheaderTextBlockStyle}" VerticalAlignment="Top" Margin="15,0,0,0" FontSize="20"/>
</Grid>
```

并将`GridViewItemTemplate`替换为以下内容:

```
<Grid Width="170">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition/>
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition/>
    </Grid.ColumnDefinitions>
    <Rectangle Height="150" Width="150" Fill="{Binding color}" Margin="0,0,20,0" HorizontalAlignment="Left" VerticalAlignment="Top"/>
    <TextBlock x:Name="nameTB" Style="{StaticResource ListViewItemTextBlockStyle}" FontSize="20" Text="{Binding name}" Grid.Row="1" HorizontalAlignment="Left" VerticalAlignment="Center" TextWrapping="WrapWholeWords"/>
    <TextBlock x:Name="priceTB" TextWrapping="Wrap" Style="{StaticResource ListViewItemTextBlockStyle}" FontSize="20" Text="{Binding price}" Grid.Row="2" HorizontalAlignment="Left" Margin="0,0,0,5"/>
    <TextBlock x:Name="ratingTB" TextWrapping="Wrap" Grid.Row="2" Style="{StaticResource ListViewItemContentTextBlockStyle}" FontSize="20" Text="{Binding rate}" HorizontalAlignment="Right" Margin="0,0,20,5"/>
</Grid>
```

注意，我在几个标签中使用了‘binding ’,但是标签如何知道绑定到哪个属性呢？为了告诉编译器`{Binding name}`应该等于应用程序的名称，我们需要在页面上添加一个`DataContext`。

```
<Page.DataContext>
    <local:ViewModel />
</Page.DataContext>
```

`ViewModel`被用作一个通用模型，因为在某些情况下我们可能需要在同一个页面中同时实现一个`GridView`和一个`ListView`，但是只能有一个`DataContext`。

## 添加项目

我们已经有了添加列表项的代码，但是还没有向用户展示这个选项。为此，在`PageResources`标签后添加一个`AppBarButton`:

```
<Page.BottomAppBar>
    <CommandBar>
        <AppBarButton Icon="Add" Label="Add item" Click="AppBarButton_Click"/>
    </CommandBar>
</Page.BottomAppBar>
```

我们在按钮上有一个 click 事件，它设置了 *.xaml.cs* 文件中的处理程序。

```
private void AddAppBarButton_Click(object sender, RoutedEventArgs e)
{
    ListViewModel.AddItem();
}
```

同样需要添加到调用为`GridViewModel.AddItem();`的`GridView`

最后一步是告诉视图从哪里获取项目，这样它就可以用指定的模板显示它们。在`ListView`或`GridView`标签内添加相应的项目源。

```
ItemsSource="{Binding listViewModel.ListViewItems}"
```

和

```
ItemsSource="{Binding gridViewModel.ListViewItems}"
```

运行项目以查看目前的结果。您应该能够添加项目。

![ListView](img/b3e21bd818fd0e682107db2575482ab6.png)

![GridView](img/5e4c538724af8041e50a4e0792f52071.png)

## 重新排序项目

有时，您可能希望能够对列表中的项目进行重新排序。这需要对视图进行一些更改。

首先，向`ListView`和`GridView`添加以下属性。

```
CanReorderItems="True" CanDragItems="True" AllowDrop="True"
```

第一个增加了改变项目顺序的可能性，但是在一个设备上你必须拖拽项目并放到新的位置，所以属性应该设置为`true`。对于 Windows 设备来说，这已经足够了，但在 Windows Phone 上这并不可行，因为重新排序模式在默认情况下是禁用的。

我添加了两个事件处理程序，`Holding`和`Tapped`。第一个用于在用户按住其中一个项目时启用重新排序模式，第二个将禁用它，将项目捕捉到位。

`listview`的代码是:

```
private void listView_Holding(object sender, HoldingRoutedEventArgs e)
{
    listView.ReorderMode = ListViewReorderMode.Enabled;
}

private void listView_Tapped(object sender, TappedRoutedEventArgs e)
{
    listView.ReorderMode = ListViewReorderMode.Disabled;
}
```

对于`gridview`:

```
private void listView_Holding(object sender, HoldingRoutedEventArgs e)
{
    gridView.ReorderMode = ListViewReorderMode.Enabled;
}

private void listView_Tapped(object sender, TappedRoutedEventArgs e)
{
    gridView.ReorderMode = ListViewReorderMode.Disabled;
}
```

通过按住一个项目直到它开始浮动来运行和测试。然后再次按住该项目以将其拖到任何地方。定位后，点击任一项目完成。

![ListViewReordering](img/ff67c93afad64ab066296d89879464e4.png)

![GridViewReordering](img/fd999d40b813b7e6b07f923017149175.png)

## 结论

如你所见，结果看起来不错。`GridViews`和`ListViews`让大量物品以优雅的方式展示变得简单。本教程需要几个步骤才能开始，但将来建立列表会更快更简单，并且是许多应用程序的重要组成部分。

感谢您抽出时间。愿代码与你同在！请在下面添加您的意见或问题。

## 分享这篇文章