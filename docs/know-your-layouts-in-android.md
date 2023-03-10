# 了解你在 Android 中的布局

> 原文：<https://www.sitepoint.com/know-your-layouts-in-android/>

Android 为基于 UI 的应用程序的开发提供了坚实的支持。Android 提供了各种小部件，应用程序程序员可以使用它们来创建所需的布局和界面。这些布局元素可以通过编程语言直接创建，也可以通过 XML 布局文件创建。在本文中，我们将向您展示这两种方法，并强调它们的区别。

### Android 中基于 XML 的布局

*在 Android 中，基于 XML 的布局是一个文件，它定义了要在 UI 中使用的不同小部件以及这些小部件和它们的容器之间的关系。Android 将布局文件视为资源。因此，布局保存在文件夹 reslayout 中。如果您使用的是 eclipse，它会在 reslayout 文件夹中创建一个默认的 xml 布局文件(main.xml ),类似于下面的 XML 代码。布局文件充当 Android 资产打包工具(AAPT)工具的输入，该工具为所有资源创建一个 R.java 文件。*

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    >
<TextView
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:text="@string/hello"
    />
</LinearLayout>
```

### 基于 XML 的布局的好处

如果你在编译时知道 UI 组件，基于 XML 的布局是非常有用的。如果需要运行时 UI 组件，那么可以使用 XML 代码添加这些组件。

基于 XML 的布局有以下优点:

*   XML 是一种非常流行和广泛使用的格式。因此，许多开发人员对此相当满意。
*   它有助于提供 UI 与代码逻辑的分离。这提供了改变一个而不影响另一个的灵活性。
*   生成 XML 输出比直接编写代码更容易，因此更容易使用拖放式 UI 工具来为 android 应用程序生成界面。

### Android 中的标准布局

*Android 中的 UI 是视图组和视图的层次结构。视图组将是层次结构中的中间节点，而视图将是终端节点。*

 *例如，在上面的 main.xml 文件中，LinearLayout 是一个 viewgroup，TextView 是一个视图。

Android 提供了以下标准布局(视图组),可以在您的 Android 应用程序中使用。

*   绝对布局
*   框架布局
*   线性布局
*   相对布局
*   表格布局

现在，我们将详细探讨其中的每一个。

### 绝对布局

在绝对布局中，我们可以指定想要放置的每个控件的精确坐标。在绝对布局中，我们将给出每个控件的精确 X 和 Y 坐标。以下是绝对布局的示例:

```
<?xml version="1.0" encoding="utf-8"?>
<AbsoluteLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">

    <TextView
    	android:layout_x="10px"
    	android:layout_y="110px"
    	android:text="@string/username"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content" />
    <EditText
    	android:layout_x="150px"
    	android:layout_y="100px"
    	android:width="150px"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content" />
    <TextView
    	android:layout_x="10px"
    	android:layout_y="160px"
    	android:text="@string/password"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content" />
    	<EditText
    	android:layout_x="150px"
    	android:layout_y="150px"
    	android:width="150px"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content" />

     <Button
    	android:id="@+id/login"
    	android:text="@string/login"
    	android:layout_x="75px"
    	android:layout_y="200px"
    	android:width="200px"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content" />
</AbsoluteLayout>
```

在本例中，我们创建了两个 TextViews、两个 EditTexts 和一个 button。要创建绝对布局，您必须在布局的 XML 文件中使用<absolutelayout>标签。</absolutelayout>

然后，我们创建控件，并在控件上指定 android:layout_x 和 android:layout_y 属性，以给出元素在屏幕上的绝对位置。如果我们用这种布局运行应用程序，它应该是这样的:

[![](img/a562f581f1049abac81f479570769bbb.png "android1")](https://www.sitepoint.com/wp-content/uploads/2012/04/android1.jpg)

使用绝对布局时，我们可以指定元素的确切位置。您可以通过更改控件中的 android:layout_x 和 android:layout_y 属性来移动它们。

绝对布局现在实际上已经被废弃了，因为它对于各种新的 Android 设备来说不够灵活。因此，只有在没有其他类型的布局适合您的目的时，才应该使用它。

### 框架布局

*当你想在每个屏幕上显示一个项目时使用框架布局。使用框架布局，我们可以有多个项目，但它们会重叠，一次只显示一个。*

 *当您想要在屏幕上创建动画或运动时，FrameLayout 特别有用。

现在，我们将创建一个使用框架布局的小应用程序，它有一个按钮和一个文本视图。

首先，显示按钮。点击按钮后，将显示文本视图。以下是布局的代码:

```
<FrameLayout
	android:layout_width="fill_parent"
	android:layout_height="fill_parent"
	xmlns:android="http://schemas.android.com/apk/res/android">

	<Button
		android:id="@+id/username"
    	android:text="@string/username"
		android:layout_height="fill_parent"
		android:layout_width="fill_parent"
		android:textSize="30sp"
		android:onClick="onClick"
		android:gravity="center" />
	<TextView
		android:id="@+id/password"
    	android:text="@string/password"
		android:layout_height="fill_parent"
		android:layout_width="fill_parent"
		android:textSize="30sp"
		android:visibility="gone"
		android:gravity="center" />
</FrameLayout>
```

这里，我们有一个按钮点击处理程序叫做 onClick。TextView 具有属性 android:visibility= *“消失”，*，它隐藏了 TextView。该活动的代码如下:

```
package com.FrameLayoutDemo;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;

public class FrameLayoutDemo extends Activity {
    /** Called when the activity is first created. */

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

    }

    public void onClick(View v) {

    	View vUsername = findViewById(R.id.username);
    	vUsername.setVisibility(View.GONE);

    	View vPassword = findViewById(R.id.password);
    	vPassword.setVisibility(View.VISIBLE);

      }
}
```

如果我们运行这个框架布局程序，它将如下所示:

[![](img/2755e6d80decb9d290fba27bce8a3726.png "android2")](https://www.sitepoint.com/wp-content/uploads/2012/04/android2.jpg)

当我们点击按钮时，onClick 函数被调用。在 onClick 函数中，我们获得了用户名和密码控件，并使用 setVisibility 函数将用户名的可见性设置为“消失”,将密码的可见性设置为“可见”。

[![](img/dda0fdf8eb2bb3ed6c4ecef5e3723665.png "android3")](https://www.sitepoint.com/wp-content/uploads/2012/04/android3.jpg)

### 线性布局

线性布局用于在每行放置一个元素。因此，所有的元素都将被有序地从上到下放置。这是在 Android 上创建表单的一种非常广泛使用的布局。我们现在要创建一个小应用程序，使用线性布局显示一个基本的表单。layout.xml 文件如下所示:

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	android:orientation="vertical"
	android:layout_width="fill_parent"
    android:layout_height="fill_parent">

    <TextView
    	android:text="@string/username"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content" />
    <EditText
    	android:width="150px"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content" />
    <TextView
    	android:text="@string/password"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content" />
    <EditText
    	android:width="150px"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content" />
</LinearLayout>
```

在本设计中，我们创建了一个垂直线性布局，显示用户名和密码，如下所示:

[![](img/233377dec8f049933e9f33e7ca5de4e5.png "android4")](https://www.sitepoint.com/wp-content/uploads/2012/04/android4.jpg)

我们甚至可以通过使 Android:orientation = " horizontal "来改变我们的线性布局的方向。这将在一条水平线中显示所有控件，如下所示:

[![](img/0bee63b49e8f0677a09ebdc9bba6e620.png "android5")](https://www.sitepoint.com/wp-content/uploads/2012/04/android5.jpg)

### 相对布局

使用相对布局，我们可以指定元素相对于其他元素或相对于父容器的位置。

为了指定相对于其父容器的位置，我们使用 Android:layout _ alignParentTop =*" true "*和 Android:layout _ alignParentLeft =*" true "*将元素对齐到父容器的左上角。然后，为了与另一个元素对齐，我们可以使用属性 Android:layout _ align left =*" @+id/other element "*和 Android:layout _ below =*" @+id/other element "。*

下面是一个应用程序，它采用了基本表单的相对布局，包含用户名字段、密码字段和登录按钮:

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
      android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    xmlns:android="http://schemas.android.com/apk/res/android"
    >
    <TextView
        android:id="@+id/username"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/username"
        android:layout_alignParentTop="true"
        android:layout_alignParentLeft="true"
        />
    <EditText
    	android:id="@+id/txtusername"
        android:layout_width="fill_parent"
        android:layout_height="50px"
        android:layout_alignLeft="@+id/username"
        android:layout_below="@+id/username"
        android:layout_centerHorizontal="true"
        />

       <TextView
        android:id="@+id/password"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/password"
      android:layout_alignLeft="@+id/txtusername"
        android:layout_below="@+id/txtusername"
        android:layout_centerHorizontal="true"
        />
    <EditText
    	android:id="@+id/txtpassword"
        android:layout_width="fill_parent"
        android:layout_alignLeft="@+id/password"
        android:layout_height="50px"
        android:layout_below="@+id/password"
        android:layout_centerHorizontal="true"
        />

        <Button
    	android:id="@+id/login"
    	android:text="@string/login"
    	android:width="200px"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content"
    	android:layout_alignLeft="@+id/txtpassword"
        android:layout_below="@+id/txtpassword"
        android:layout_centerHorizontal="true"
         />

</RelativeLayout>
```

在这种布局中，TextView 用户名位于屏幕的左上角，使用了上述相同的属性:

*   Android:layout _ alignParentTop =*" true "*
*   Android:layout _ alignParentLeft =*" true "*

然后，我们使用属性定位下面的其他元素:

*   Android:layout _ align left =*" @+id/txt password "*
*   Android:layout _ below =*" @+id/txt password "*
*   Android:layout _ center horizontal =*" true "*

如果我们运行这个应用程序，它将看起来像下面的图像:

[![](img/ba6757afc286cfa00eb9d4c37fad982c.png "android6")](https://www.sitepoint.com/wp-content/uploads/2012/04/android6.jpg)

### 表格布局

使用 table layout，我们创建一个包含行和列的表格，并在其中放置元素。在每一行中，您可以指定一个或多个元素。

使用 TableLayout 中的标签<tablerow>创建表格行。</tablerow>

以下是一个带有表格布局的应用程序示例:

```
<TableLayout
	android:layout_width="fill_parent"
	android:layout_height="fill_parent"
	xmlns:android="http://schemas.android.com/apk/res/android">

	<TableRow>
		<TextView
        android:id="@+id/username"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/username"

        />
    <EditText
    	android:id="@+id/txtusername"
        android:layout_width="fill_parent"
        android:layout_height="50px"
        android:width="100px"

        />
	</TableRow>
	<TableRow>
		<TextView
        android:id="@+id/password"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/password"

        />
    <EditText
    	android:id="@+id/txtpassword"
        android:layout_width="fill_parent"
         android:layout_height="50px"
         android:width="100px"

        />
	</TableRow>
	<TableRow>
		<Button
    	android:id="@+id/login"
    	android:text="@string/login"
    	android:width="200px"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content"
    	 />
	</TableRow>
</TableLayout>
```

在本例的第一行，我们添加了两个元素:用户名 TextView 和 EditText。在第二行中，我们添加了另外两个元素:密码 TextView 和 EditText。然后，在第三行，我们添加了登录按钮。

此应用程序的输出如下:

[![](img/45c9a6fb1cea0c0886ca1f760d8ecbed.png "android7")](https://www.sitepoint.com/wp-content/uploads/2012/04/android7.jpg)

### 结论

Android 提供了一种非常灵活的方式，使用基于 XML 的布局来显示布局。这有助于我们创建应用程序，同时保持布局和逻辑完全分离。这使得我们很容易改变布局，即使在应用程序编写完成后，也不需要改变编程逻辑。

Android 也有多种布局，可以用来创建更复杂的 ui，以满足各种应用程序的需求。所以，享受使用不同的 Android 布局为你的应用程序创建漂亮的 UI 吧。*** 

## **分享这篇文章**