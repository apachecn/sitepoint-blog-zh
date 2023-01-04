# 黑仔的方式来显示在 Android 收集小部件的项目列表

> 原文：<https://www.sitepoint.com/killer-way-to-show-a-list-of-items-in-android-collection-widget/>

在 Android 的早期版本中，应用程序小部件只能显示类似于`TextView`、`ImageView`等视图。但是如果我们想在我们的小部件中显示一个项目列表呢？例如，显示整个下周的温度信息列表。Android 3.0 中引入了收集小部件来提供这一额外的好处。收藏小部件支持`ListView`、`GridView`和`StackView`布局。

今天，我将帮助您了解收集小部件是如何工作的。我们将为 Todo 应用程序构建一个应用程序小部件。收集小部件将用于显示未决任务的列表。

我假设你已经知道如何制作一个基本的应用程序部件。如果没有，请参考[这篇文章](https://www.sitepoint.com/how-to-code-an-android-widget/)，当您准备好构建自己的收藏小部件时再回来。

## 入门指南

请在此下载起始项目代码[，因为我们将从它开始构建。](https://github.com/indiandollar/CollectionWidgetTutorial-Starter)

代码已经实现了一个基本的小部件，我们将在同一个项目中创建一个集合小部件。一个基本的小部件显示未决任务的数量，收集小部件将显示完整的列表。要制作集合小部件，除了基本组件之外，还需要两个主要组件:

*   `RemoteViewsService`
*   `RemoteViewsFactory`

让我们了解一下这些组件是做什么的。

## 使用`RemoteViewsFactory`

`RemoteViewsFactory`在小部件的上下文中充当适配器的角色。适配器用于将集合项(例如，ListView 项或 GridView 项)与数据集连接起来。
让我们将这个类添加到我们的项目中。创建一个新的 Java 类，命名为**MyWidgetRemoteViewsFactory**，并将其设置为实现类`RemoteViewsService.RemoteViewsFactory`。

```
public class MyWidgetRemoteViewsFactory implements RemoteViewsService.RemoteViewsFactory {

    private Context mContext;
    private Cursor mCursor;

    public MyWidgetRemoteViewsFactory(Context applicationContext, Intent intent) {
        mContext = applicationContext;
    }

    @Override
    public void onCreate() {

    }

    @Override
    public void onDataSetChanged() {

        if (mCursor != null) {
            mCursor.close();
        }

        final long identityToken = Binder.clearCallingIdentity();
        Uri uri = Contract.PATH_TODOS_URI;
        mCursor = mContext.getContentResolver().query(uri,
                null,
                null,
                null,
                Contract._ID + " DESC");

        Binder.restoreCallingIdentity(identityToken);

    }

    @Override
    public void onDestroy() {
        if (mCursor != null) {
            mCursor.close();
        }
    }

    @Override
    public int getCount() {
        return mCursor == null ? 0 : mCursor.getCount();
    }

    @Override
    public RemoteViews getViewAt(int position) {
        if (position == AdapterView.INVALID_POSITION ||
                mCursor == null || !mCursor.moveToPosition(position)) {
            return null;
        }

        RemoteViews rv = new RemoteViews(mContext.getPackageName(), R.layout.collection_widget_list_item);
        rv.setTextViewText(R.id.widgetItemTaskNameLabel, mCursor.getString(1));

        return rv;
    }

    @Override
    public RemoteViews getLoadingView() {
        return null;
    }

    @Override
    public int getViewTypeCount() {
        return 1;
    }

    @Override
    public long getItemId(int position) {
        return mCursor.moveToPosition(position) ? mCursor.getLong(0) : position;
    }

    @Override
    public boolean hasStableIds() {
        return true;
    }

} 
```

在上面的代码中，**MyWidgetRemoteViewsFactory**覆盖了`RemoteViewsFactory`类中的一些方法:

*   第一次创建 appwidget 时调用`onCreate`。
*   每当更新 appwidget 时，就会调用`onDataSetChanged`。
*   `getCount`返回光标中的记录数。(在我们的例子中，需要在应用程序小部件中显示的任务项目的数量)
*   处理所有的处理工作。它返回一个`RemoteViews`对象，在我们的例子中是单个列表项。
*   `getViewTypeCount`返回我们在`ListView`中拥有的视图类型的数量。在我们的例子中，我们在每个`ListView`项目中有相同的视图类型，所以我们在那里`return 1`。

## 使用`RemoteViewsService`

`RemoteViewsService`的主要目的是返回一个`RemoteViewsFactory`对象，该对象进一步处理用适当的数据填充小部件的任务。这门课没什么内容。

创建一个名为**MyWidgetRemoteViewsService**的新类，扩展类`RemoteViewsService`。

```
public class MyWidgetRemoteViewsService extends RemoteViewsService {
    @Override
    public RemoteViewsFactory onGetViewFactory(Intent intent) {
        return new MyWidgetRemoteViewsFactory(this.getApplicationContext(), intent);
    }
} 
```

与 android 中的所有其他服务一样，我们必须在 manifest 文件中注册这个服务。

```
<service android:name=".AppWidget.MyWidgetRemoteViewsService"
	android:permission="android.permission.BIND_REMOTEVIEWS"></service>
```

注意特殊权限**Android . permission . bind _ remote views**。这允许系统绑定您的服务来为每一行创建微件视图，并防止其他应用程序访问您的微件数据。

## 启动`RemoteViewsService`

既然我们已经设置了额外的组件，是时候创建`WidgetProvider`来调用`RemoteViewsService`了。

在 AppWidget 包中新建一个类，命名为**CollectionAppWidgetProvider**:

```
@Override
public void onUpdate(Context context, AppWidgetManager appWidgetManager, int[] appWidgetIds) {
    for (int appWidgetId : appWidgetIds) {
        RemoteViews views = new RemoteViews(
                context.getPackageName(),
                R.layout.collection_widget
        );
        Intent intent = new Intent(context, MyWidgetRemoteViewsService.class);
        views.setRemoteAdapter(R.id.widgetListView, intent);
        appWidgetManager.updateAppWidget(appWidgetId, views);
    }
} 
```

## 创建小部件布局

现在在 *res/xml* 中创建一个新的资源文件，并将其命名为 **collection_widget.xml** 。

在这个文件中，我们定义了小部件设置，比如小部件应该使用哪个布局文件，并添加了一个预览图像以获得更好的用户体验。

```
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    android:minWidth="40dp"
    android:minHeight="40dp"
    android:updatePeriodMillis="864000"
    android:previewImage="@drawable/simple_widget_preview"
    android:initialLayout="@layout/collection_widget"
    android:resizeMode="horizontal|vertical"
    android:widgetCategory="home_screen">
</appwidget-provider> 
```

再创建一个资源文件，但这次是在 *res/layout* 中，并将其命名为**collection _ widget . XML**
在这个文件中，我们定义了我们想要在集合小部件中显示的内容的布局。我们将在顶部放置一个标题，然后在底部放置`ListView`来显示任务列表。

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="@color/colorWhite"
            xmlns:tools="http://schemas.android.com/tools"
            android:orientation="vertical">
    <FrameLayout android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <TextView android:layout_width="match_parent"
            android:id="@+id/widgetTitleLabel"
            android:text="@string/title_collection_widget"
            android:textColor="@color/colorWhite"
            android:background="@color/colorPrimary"
            android:textSize="18dp"
            android:gravity="center"
            android:textAllCaps="true"
            android:layout_height="@dimen/widget_title_min_height"></TextView>
    </FrameLayout>
    <LinearLayout android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <ListView android:id="@+id/widgetListView"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="@color/colorWhite"
            android:dividerHeight="1dp"
            android:divider="#eeeeee"
            tools:listitem="@layout/collection_widget_list_item"></ListView>
    </LinearLayout>
</LinearLayout>
```

我们需要在 **res/layout** 中再创建一个文件来定义每个列表项的布局。

创建这个文件并将其命名为**collection _ widget _ list _ item . XML**

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:paddingLeft="@dimen/widget_listview_padding_x"
    android:paddingRight="@dimen/widget_listview_padding_x"
    android:paddingStart="@dimen/widget_listview_padding_x"
    android:paddingEnd="@dimen/widget_listview_padding_x"
    android:minHeight="@dimen/widget_listview_item_height"
    android:weightSum="2"
    android:id="@+id/widgetItemContainer"
    android:layout_height="wrap_content">

    <TextView android:id="@+id/widgetItemTaskNameLabel"
        android:layout_width="wrap_content"
        android:gravity="start"
        android:layout_weight="1"
        android:textColor="@color/text"
        android:layout_gravity="center_vertical"
        android:layout_height="wrap_content"></TextView>

</LinearLayout> 
```

现在运行应用程序，您应该能够看到填充了 todo 项目的小部件。(请确保重新安装应用程序以查看更改。也可以禁用 Android Studio 中的即时运行选项)。

## 手动更新小部件

逻辑是这样的:每当你创建一个新的 todo 项目，你必须发送一个广播到`WidgetProvider`。
在**集合 AppWidgetProvider** 类中定义一个新方法。

```
public static void sendRefreshBroadcast(Context context) {
    Intent intent = new Intent(AppWidgetManager.ACTION_APPWIDGET_UPDATE);
    intent.setComponent(new ComponentName(context, CollectionAppWidgetProvider.class));
    context.sendBroadcast(intent);
} 
```

然后覆盖**CollectionAppWidgetProvider**类中的`onReceive`方法，

```
@Override
public void onReceive(final Context context, Intent intent) {
    final String action = intent.getAction();
    if (action.equals(AppWidgetManager.ACTION_APPWIDGET_UPDATE)) {
        // refresh all your widgets
        AppWidgetManager mgr = AppWidgetManager.getInstance(context);
        ComponentName cn = new ComponentName(context, CollectionAppWidgetProvider.class);
        mgr.notifyAppWidgetViewDataChanged(mgr.getAppWidgetIds(cn), R.id.widgetListView);
    }
    super.onReceive(context, intent);
} 
```

当创建新的 todo 任务时，调用在**CollectionAppWidgetProvider**类中定义的`sendRefreshBroadcast`方法。

在`MainActivity`中，相应修改`addTodoItem`方法。

```
a.runOnUiThread(new Runnable() {
    @Override
    public void run() {
        Toast.makeText(mContext, "New task created", Toast.LENGTH_LONG).show();
        getTodoList();
        // this will send the broadcast to update the appwidget
        CollectionAppWidgetProvider.sendRefreshBroadcast(mContext);
    }
}); 
```

## 小部件中的事件处理

在我们的小部件中，顶部有一个标题，底部有一个列表视图。所以当用户点击标题时，我们启动应用程序。当在列表视图中单击单个项目时，我们启动 details 活动。在我们的 todo 应用程序中，细节活动可能没有那么有用，但让我们通过它来理解这个概念。

### 单击单个视图上的事件

向`TextView`、`ImageView`等视图添加点击事件非常容易。下面是更新后的`onUpdate`方法的代码。

```
@Override
public void onUpdate(Context context, AppWidgetManager appWidgetManager, int[] appWidgetIds) {
    for (int appWidgetId : appWidgetIds) {
        RemoteViews views = new RemoteViews(
                context.getPackageName(),
                R.layout.collection_widget
        );

        // click event handler for the title, launches the app when the user clicks on title
        Intent titleIntent = new Intent(context, MainActivity.class);
        PendingIntent titlePendingIntent = PendingIntent.getActivity(context, 0, titleIntent, 0);
        views.setOnClickPendingIntent(R.id.widgetTitleLabel, titlePendingIntent);

        Intent intent = new Intent(context, MyWidgetRemoteViewsService.class);
        views.setRemoteAdapter(R.id.widgetListView, intent);
        appWidgetManager.updateAppWidget(appWidgetId, views);
    }
} 
```

这里的想法类似于我们如何在应用程序中添加点击事件。但是由于小部件在不同的上下文中运行，我们需要通过一个`PendingIntent`来注册点击事件。

### 点击`ListView`项目上的事件

在`ListView`项上添加点击事件并不像在`ListView`对象上设置`setOnItemClickListener`那么简单。它需要一些额外的步骤。

首先你需要为`PendingIntent`设置一个模板。将此代码添加到`views.setRemoteAdapter(R.id.widgetListView, intent);`之后的**CollectionAppWidgetProvider**类的`onUpdate`方法中

```
// template to handle the click listener for each item
Intent clickIntentTemplate = new Intent(context, DetailsActivity.class);
PendingIntent clickPendingIntentTemplate = TaskStackBuilder.create(context)
        .addNextIntentWithParentStack(clickIntentTemplate)
        .getPendingIntent(0, PendingIntent.FLAG_UPDATE_CURRENT);
views.setPendingIntentTemplate(R.id.widgetListView, clickPendingIntentTemplate); 
```

对于每个`ListView`项目，我们将启动`DetailsActivity`，它将简单地显示作为额外内容发送的任务描述。

然后在每次`RemoteViewsFactory`创建一个新的`RemoteViews`对象时填充这个模板。
将此代码添加到**MyWidgetRemoteViewsFactory**类的`getViewAt`方法中。

```
Intent fillInIntent = new Intent();
fillInIntent.putExtra(CollectionAppWidgetProvider.EXTRA_LABEL, mCursor.getString(1));
rv.setOnClickFillInIntent(R.id.widgetItemContainer, fillInIntent); 
```

在这里，我们填充在**CollectionAppWidgetProvider**类中定义的 pending intents 模板。注意，我们希望整行都是可点击的，所以我们在根元素**collection _ widget _ list _ item . XML**上设置了点击监听器

## 结论

在本文中，我试图帮助解决初学者通常面临的最常见的问题。如果你有任何问题，或者任何事情对你不起作用，请在下面的评论中告诉我。

你可以在这里下载完整的工作代码。

## 分享这篇文章