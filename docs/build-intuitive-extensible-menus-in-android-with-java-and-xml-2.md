# 用 Java 和 XML 在 Android 中构建菜单:上下文菜单和弹出菜单

> 原文：<https://www.sitepoint.com/build-intuitive-extensible-menus-in-android-with-java-and-xml-2/>

在本系列的[上一篇文章中，我们学习了如何使用 XML 或直接通过 Java 编程为您的 Android 应用程序创建标准选项菜单。除了这些典型的选项菜单，Android 开发者还可以在他们的 Android 应用中创建专门的菜单类型，如**上下文菜单**和**弹出菜单**。](https://www.sitepoint.com/menus-in-android/ "Build Intuitive, Extensible Menus in Android With Java and XML: Introduction")

**上下文菜单**是影响(或出现在)特定项目的菜单，通常具有仅影响该特定项目的选项。当用户按住特定项目时，Android 中的上下文菜单就会出现。上下文菜单将呈现给用户，他或她将能够从一系列选项中进行选择。

Android 还允许你在应用程序中创建**弹出菜单**。弹出式菜单是一种模式菜单，可以编程为在单击按钮时出现。

在本文中，我们将展示如何在我们的 Android 应用程序中创建上下文菜单和弹出菜单。

### 注册上下文菜单视图

首先，我们将通过在单个项目上创建上下文菜单来构建一个基本示例。上下文菜单是在一个视图中创建的，所以我们将首先为上下文菜单注册那个视图，然后继续菜单本身。

首先，让我们在 layout main.xml 文件中创建一个 Android 活动，如下所示:

```
<?xml version="1.0" encoding="utf-8"?>

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

android:layout_width="fill_parent"

android:layout_height="fill_parent"

android:orientation="vertical"

>

<TextView

android:id="@+id/<span style="text-decoration: underline;">Username</span>"

android:layout_width="fill_parent"

android:layout_height="60dip"

android:text="Hold For contextual menu"

android:padding="4dip"

/>

<EditText

android:id="@+id/usernameEdittext"

android:layout_height="wrap_content"

android:singleLine="true"

android:background="#<span style="text-decoration: underline;">ffffff</span>"

android:layout_width="400dip" >

</EditText>

</LinearLayout>
```

在上面的布局中，我们刚刚在布局中创建了一个 TextView 和一个 edit 视图。现在，我们将为该活动编写如下代码:

[sourcecode language="java"]

包 com . contexualandpopupmenudemon；

导入 Android . OS . bundle；

导入 Android . app . activity；

导入 Android . view . menu；

导入 Android . widget . edittext；

公共类 ContextualAndPopupmenuDemo 扩展 Activity {

@覆盖

受保护的 void onCreate(Bundle saved instancestate){

超级。oncreate(savedinstancestat)：

setContentView(r . layout . main)；

EditText UserNameEditText = (EditText)findViewById(R.id.usernameEdittext);

registerForContextMenu(UserNameEditText)；

}

}

[/sourcecode]

在上面的活动中，我们覆盖了 onCreate 方法，在该方法中我们设置了主布局。然后，我们使用函数 findViewById 找到 EditText。

一旦我们获得了 EditView，我们就在那个视图上调用 registerForContextMenu。该函数注册视图，并允许我们显示上下文菜单。

### 创建上下文菜单

一旦我们为上下文菜单注册了视图，我们将为上下文菜单创建一个 XML 格式的菜单。要创建 XML 格式的菜单，请在资源的 menu 文件夹中创建一个 contextual.xml 文件。将以下代码添加到 contextual.xml 文件中:

```
<menu xmlns:android="http://schemas.android.com/apk/res/android">

<item android:id="@+id/fetch"

android:title="Fetch New <span style="text-decoration: underline;">Username</span>" />

<item android:id="@+id/check"

android:title="Check For Duplicate" />

</menu>
```

在上下文菜单中，我们创建了两个项目:一个用于获取用户名，另一个用于检查重复项。一旦我们创建了 XML 格式的菜单，我们就必须覆盖 onCreateContextMenu 函数，如下所示:

[sourcecode language="java"]

@覆盖

public void onCreateContextMenu(上下文菜单、视图 v、

ContextMenuInfo menuInfo) {

super.onCreateContextMenu(menu，v，menu info)；

if(v . getid()= = r . id . usernameedittext){

getMenuInflater()。inflate(R.menu.contextual，menu)；

}

}

[/sourcecode]

在这段代码中，我们确定视图的 ID 是否是我们正在寻找的特定 ID。如果是，我们将启动菜单，如本系列前一篇文章中的[所述。](https://www.sitepoint.com/menus-in-android/ "Build Menus in Android with Java and XML: Introduction")

现在，如果您在编辑文本上停留几秒钟，您应该能够看到以下上下文菜单:

![image001](img/d0c27a9641b7a6c0dd58296ab271e5d8.png)

如果您不想在 XML 中构建这个菜单，您可以使用下面的编程在 Java 中构建一个相同的菜单:

[sourcecode language="java"]

@覆盖

public void onCreateContextMenu(上下文菜单、视图 v、

ContextMenuInfo menuInfo) {

super.onCreateContextMenu(menu，v，menu info)；

if(v . getid()= = r . id . usernameedittext){

menu.add(0，1，0，"获取新用户名")；

menu.add(0，2，0，"检查重复")；

}

}

[/sourcecode]

现在，如果您再次运行该应用程序，您将看到一个与我们通过 XML 格式创建的菜单非常相似的菜单。

### 响应上下文菜单上的点击

为了响应用户在上下文菜单上的选择，我们必须在活动中实现 onContextItemSelected 函数。为了响应我们的菜单项，我们实现了 onContextItemSelected，如下所示:

[sourcecode language="java"]

@覆盖

public boolean oncontextitems selected(MenuItem item){

switch (item.getItemId()) {

案例识别提取:

showToast(" Fetch Clicked ")；

返回 true

案例编号检查:

showToast(" Check Clicked ")；

返回 true

默认值:

返回 super . oncontextitemselected(item)；

}

}

public void showToast(字符串消息){

Toast Toast = Toast . make text(getApplicationContext()，message，Toast。LENGTH _ SHORT)；

toast . show()；

}

[/sourcecode]

在上面的编程中，被选择的菜单项被传递给 onContextItemSelected 函数，我们可以调用 getItemId 来识别哪个菜单项被单击。然后，我们调用适当的函数(匹配用户的选择)来处理这个菜单点击。目前，选择菜单选项产生的唯一功能是带有消息的 toast。现在，如果您单击“获取新用户名”选项，您应该会看到以下内容:

![image003](img/f3cfed6ce0c7071656269e67df48f71f.png)

### 创建弹出菜单

弹出菜单(仅在 Android API 级别 11 之后支持)是一个模态菜单，您可以在视图附近创建和显示它。出于演示目的，我们将在活动上单击“选项”按钮时创建一个弹出菜单。首先，让我们将这个“选项”按钮添加到我们的 layout.xml 文件中，如下所示。

```
<Button

android:id="@+id/<span style="text-decoration: underline;">btnoptions</span>"

android:layout_width="wrap_content"

android:layout_height="wrap_content"

android:text="Options"

android:onClick="CreatePopupMenu"/>
```

这将为我们的弹出菜单创建必要的“选项”按钮。选中时，将调用 CreatePopupMenu 函数。然后，我们在 res/menu 文件夹中创建一个新的 popup.xml 文件，包含以下内容:

```
<menu xmlns:android="http://schemas.android.com/apk/res/android">

<item android:id="@+id/option1"

android:title="Option 1" />

<item android:id="@+id/option2"

android:title="Option 2" />

</menu>
```

在上面的 popup.xml 文件中，我们创建了两个菜单项:option1 和 option2。然后，我们使用下面的编程将函数 CreatePopupMenu 添加到我们的活动中:

[sourcecode language="java"]

public void CreatePopupMenu(视图 v) {

PopupMenu mypopupmenu = new PopupMenu(this, v);

MenuInflater inflater = mypopupmenu.getMenuInflater();

inflater.inflate(R.menu. *popup* , mypopupmenu.getMenu());

mypopupmenu.show();

}

[/sourcecode]

上面，我们创建了一个新的 PopupMenu 对象，然后在其中包含了我们的 popup.xml 菜单。然后，我们通过调用 *show* 函数来显示弹出菜单。

现在，如果我们运行应用程序并点击我们新的“选项”按钮，您应该能够看到如下所示的菜单。

![image005](img/2e3ce77b91fc6f40e29553319f0cfbe3.png)

### 响应弹出菜单点击

为了响应弹出菜单的点击，您必须设置一个菜单项 click listener，这是一个实现 OnMenuItemClickListener 接口的类。它有一个方法—onMenuItemClick。我们将在活动本身中实现这个接口。因此，我们必须将类定义更改如下:

[sourcecode language="java"]

公共类 ContextualAndPopupmenuDemo 扩展了 Activity implements OnMenuItemClickListener {

然后，我们更改 CreatePopupMenu，并在菜单项上实现单击，如下所示

public void CreatePopupMenu(视图 v) {

PopupMenu mypopupmenu = new PopupMenu(this, v);

mypopupmenu . setonmenuitemclicklistener(this)；

MenuInflater inflater = mypopupmenu.getMenuInflater();

inflater.inflate(R.menu.popup, mypopupmenu.getMenu());

mypopupmenu.show();

}

@覆盖

public boolean onMenuItemClick(MenuItem arg 0){

开关(arg0.getItemId()) {

案例编号选项 1:

show toast(" option 1 Clicked ")；

返回 true

案例编号选项 2:

show toast(" option 2 Clicked ")；

返回 true

默认值:

返回 super . oncontextitemselected(arg 0)；

}

}

[/sourcecode]

在函数中，onMenuItemClick—取决于所单击的菜单项—将显示适当的 toast。如果我们点击其中一个菜单选项，我们应该会看到下面显示的吐司。

![image008](img/278fbb59849774595df1317aa0cd61e0.png)

### 结论

在 Android 中，你可以为不同的设置和环境创建各种各样的菜单。您可以在 XML 文件中定义这些菜单，也可以通过 java 代码直接添加它们。您可以根据您创建的菜单类型(上下文菜单、弹出菜单)来响应菜单项。当您提供应用程序范围的设置时，通常应该使用“选项”菜单。如果你想为一个特定的项目提供专门的选项或设置，那么你可能应该使用上下文菜单。

为了使上下文菜单出现，用户必须在期望的选项上保持一段时间。如果你想在其他用户操作上显示一个菜单，比如点击一个按钮或者点击一个图像，你也可以使用弹出菜单。不管你的应用想法或功能是什么，你现在已经拥有了捕捉用户输入和构建合理、直观的应用体验所需的工具。在你的下一个 Android 应用中实现这些不同的菜单类型时，享受乐趣。

## 分享这篇文章