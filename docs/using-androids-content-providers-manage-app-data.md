# 使用 Android 的内容提供商来管理应用数据

> 原文：<https://www.sitepoint.com/using-androids-content-providers-manage-app-data/>

在我的上一篇文章中，我创建了一个 Android [Todo 应用程序](https://www.sitepoint.com/starting-android-development-creating-todo-app/#comments)，这是一个用于移动开发的经典“Hello World”应用程序的更高级版本。在那篇文章中，我介绍了为 Android 构建视图，为按钮点击等事件分配方法(函数)，以及使用嵌入式 SQLite 数据库来存储、检索和删除数据(任务)。

在该教程中，我必须编写 SQL 查询来实现这一点，但是有一种更简单的方法，**内容提供者**。

## 为 Android 提供内容

内容提供者是管理存储在嵌入式 SQLite 数据库中的数据的一种更简单的方式。它们是连接一个进程中的数据和另一个进程中运行的代码的标准接口。它们可能看起来很难理解或实现，但事实并非如此。在本教程中，我将向您展示如何创建自己的内容提供者。

如果你不打算与其他应用程序共享你的数据，你不需要开发自己的提供商。但是，您确实需要自己的提供者在您自己的应用程序中提供定制的搜索建议。如果您想将复杂的数据或文件从您的应用程序复制并粘贴到其他应用程序，您还需要自己的提供程序。

要开始跟随教程，从 GitHub 下载[项目的当前 repo，并将其导入您的 IDE。](https://github.com/sitepoint-examples/TodoList)

在编写内容提供者类之前，我们必须向`TaskContract`类添加一些代码。将代码添加到类中现有变量声明的下面:

```
public static final String AUTHORITY = "com.example.TodoList.mytasks";
public static final Uri CONTENT_URI = Uri.parse("content://"+AUTHORITY+"/"+ TABLE);
public static final int TASKS_LIST = 1;
public static final int TASKS_ITEM = 2;
public static final String CONTENT_TYPE = ContentResolver.CURSOR_DIR_BASE_TYPE + "/example.tasksDB/"+TABLE;
public static final String CONTENT_ITEM_TYPE = ContentResolver.CURSOR_ITEM_BASE_TYPE + "/example/tasksDB" + TABLE;
```

代码只是包含必要信息的`static`常量(`final`变量)的列表。首先，我为内容提供商选择了一个权威机构。通常情况下，这将是`.provider`，但你可以选择任何东西，只要你确定它不会与任何其他应用程序冲突。

在授权之后，内容 URI 被设置为`content://com.example.TodoList.mytasks/tasks`(其中`tasks`是`TABLE`)。这个内容 URI 应该总是以`content://`开始，用于访问表中的数据。如果您将使用多个表，您可以使用与上面相同的结构，尽管您必须更改表名。

`CONTENT_TYPE`和`CONTENT_TYPE_ITEM`用于标识一个 URI 请求是指向一个目录(*即*表)还是指向一个项目(*即*表中的记录)。

我们还需要将内容提供者添加到 AndroidManifest.xml 文件中，添加以下代码:

```
<provider
android:authorities="com.example.TodoList.mytasks"
android:name=".db.TaskProvider" />
```

就在结束的`application`标签之前。

现在契约已经准备好了，我们可以创建内容提供者了。

## 创建提供者

内容提供者是一个简单的 Java 类，它扩展了 [`ContentProvider`](http://developer.android.com/reference/android/content/ContentProvider.html) 类并实现了它的方法。

我们的内容提供者将被称为`TaskProvider`，并将被放在`db`包中。为此，在名为`TaskProvider.java`的`db`文件夹中创建一个新的类文件，并添加以下代码:

```
package com.example.TodoList.db;

import android.content.ContentProvider;
import android.content.ContentValues;
import android.content.UriMatcher;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.net.Uri;

public class TaskProvider extends ContentProvider{

private SQLiteDatabase db;
private TaskDBHelper taskDBHelper;
public static final UriMatcher uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);

static {
uriMatcher.addURI(TaskContract.AUTHORITY,TaskContract.TABLE,TaskContract.TASKS_LIST);
uriMatcher.addURI(TaskContract.AUTHORITY,TaskContract.TABLE+"/#",TaskContract.TASKS_ITEM);
}

@Override
public boolean onCreate() {
return false;
}

@Override
public Cursor query(Uri uri, String[] strings, String s, String[] strings2, String s2) {
return null;
}

@Override
public String getType(Uri uri) {
return null;
}

@Override
public Uri insert(Uri uri, ContentValues contentValues) {
return null;
}

@Override
public int delete(Uri uri, String s, String[] strings) {
return 0;
}

@Override
public int update(Uri uri, ContentValues contentValues, String s, String[] strings) {
return 0;
}
}
```

此时此刻，这堂课做的并不多。它是一个内容提供者，但是它不提供任何内容，因为方法还没有实现。它所做的只是创建一个`UriMatcher`实例，然后用它来检查所访问的 URI 是否有效。在静态块中，两个 URIs 被添加到`UriMatcher`，URI 对应于表，URI 对应于记录。

让我们实现`onCreate()`方法。Android 系统在创建提供者后会立即调用这个方法。这意味着它应该尽可能简单，因为我们不希望系统在创建提供者时进行太多的计算。将这段代码放入我们刚刚创建的类的`onCreate()`方法中:

```
@Override
public boolean onCreate() {
boolean ret = true;
taskDBHelper = new TaskDBHelper(getContext());
db = taskDBHelper.getWritableDatabase();

if (db == null) {
ret = false;
}

if (db.isReadOnly()) {
db.close();
db = null;
ret = false;
}

return ret;
}
```

在这个方法中，`TaskDBHelper`类被用来创建一个助手对象，如果数据库还不存在的话，它就创建数据库。如果由于数据库不可访问而无法加载提供程序，此方法将返回 false，否则将返回 true。这段简单的代码足以创建我们的内容提供者。

现在让我们添加`query()`功能，它用于检索存储在数据库中的数据并返回一个`Cursor`实例:

```
@Override
public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder) {
SQLiteQueryBuilder qb = new SQLiteQueryBuilder();
qb.setTables(TaskContract.TABLE);

switch (uriMatcher.match(uri)) {
case TaskContract.TASKS_LIST:
break;

case TaskContract.TASKS_ITEM:
qb.appendWhere(TaskContract.Columns._ID + " = "+ uri.getLastPathSegment());
break;

default:
throw new IllegalArgumentException("Invalid URI: " + uri);
}

Cursor cursor = qb.query(db,projection,selection,selectionArgs,null,null,null);

return cursor;
}
```

`query()`方法应该返回一个`Cursor`实例，或者在查询出现问题时抛出一个异常。

在上面的方法中，使用了一个`SQLiteQueryBuilder`实例来帮助创建查询。如果(URI)请求的对象是一条记录(一个条目，而不是一个列表/表格)，那么`SQLiteQueryBuilder`使用`appendWhere()`方法将`WHERE`子句添加到查询中。最后，通过执行由查询构建器创建的查询，创建一个`Cursor`实例，然后由方法返回。如果请求的 URI 无效，则抛出`IllegalArgumentException`。

在添加了`query()`方法之后，我们将实现`insert()`方法，该方法将存储在`ContentValues`实例中的一些值作为新记录添加到一个表中:

```
@Override
public Uri insert(Uri uri, ContentValues contentValues) {

if (uriMatcher.match(uri) != TaskContract.TASKS_LIST) {
throw new IllegalArgumentException("Invalid URI: "+uri);
}

long id = db.insert(TaskContract.TABLE,null,contentValues);

if (id>0) {
return ContentUris.withAppendedId(uri,id);
}
throw new SQLException("Error inserting into table: "+TaskContract.TABLE);
}
```

该方法接收两个参数，即将要插入记录的 URI 和将要组成记录的值。如果 URI 与表的 URI 不匹配，那么抛出一个`IllegalArgumentException`。如果 URI 是正确的，那么使用一个`SQLiteDatabase`实例的`insert()`方法将数据插入到正确的表中。这个`insert()`方法返回新记录的 URI。如果`id`大于 0(这意味着记录被添加到表中)，就会这样做，并且使用`withAppendedId()`方法创建新的 URI。如果`id`不大于 0，意味着记录没有被存储，因此抛出`SQLException`。

现在让我们转到另一个方法，即`update()`方法。该方法用于更改(*即*更新)SQLite 数据库中给定表格的现有记录:

```
@Override
public int update(Uri uri, ContentValues contentValues, String s, String[] strings) {

int updated = 0;

switch (uriMatcher.match(uri)) {
case TaskContract.TASKS_LIST:
db.update(TaskContract.TABLE,contentValues,s,strings);
break;

case TaskContract.TASKS_ITEM:
String where = TaskContract.Columns._ID + " = " + uri.getLastPathSegment();
if (!s.isEmpty()) {
where += " AND "+s;
}
updated = db.update(TaskContract.TABLE,contentValues,where,strings);
break;

default:
throw new IllegalArgumentException("Invalid URI: "+uri);
}

return updated;
}
```

这个方法有四个参数:

*   `Uri uri`:要查询的 URI。它可以是单个记录的 URI，也可以是一个表的 URI。
*   `ContentValues contentValues`:一组键-值对，列名作为键，要更新的值作为值。
*   `String s`:匹配将要更新的行的选择。
*   `String[] strings`:以上各行的自变量。

使用这种方法，我们首先检查 URI 是对应于一个表还是一个记录。如果它对应于一个表，我们将`update()`方法调用到一个`SQLiteDatabase`实例，传递与我们的方法相同的参数(除了 URI，它被更改为表名)。

如果 URI 对应于一条记录，我们必须更改第三个参数，使所需的 id 与数据库中记录的 ID 相匹配，并添加用户所需的任何选择。

最后，`update()`记录被调用到`SQLiteDatabase`实例，传递表名、内容值、where 子句和最后一个参数。
该方法将返回更新的行数，或者如果传递的 URI 不正确，则抛出一个`IllegalArgumentException`。

接下来的方法是`delete()`。您会注意到这个方法与上面实现的`update()`方法没有什么不同:

```
@Override
public int delete(Uri uri, String selection, String[] selectionArgs) {

int deleted = 0;

switch (uriMatcher.match(uri)) {
case TaskContract.TASKS_LIST:
db.delete(TaskContract.TABLE,selection,selectionArgs);
break;

case TaskContract.TASKS_ITEM:
String where = TaskContract.Columns._ID + " = " + uri.getLastPathSegment();
if (!selection.isEmpty()) {
where += " AND "+selection;
}

deleted = db.delete(TaskContract.TABLE,where,selectionArgs);
break;

default:
throw new IllegalArgumentException("Invalid URI: "+uri);
}

return deleted;
}
```

就像`update()`方法一样，`delete()`方法检查传递的 URI 是否对应于一个表，如果是，就删除这个表。如果它对应于一条记录，它将从表中删除该记录。在后一种情况下，我们确保在`selection`参数中添加一个 id 子句。

如上所述，`delete()`方法将返回被删除的行数，或者如果 URI 无效，则抛出一个`IllegalArgumentException`。

为了完成我们的`TaskProvider`类，我们必须实现最后一个方法，即`getType()`方法。该方法将判断传递的 URI 是对应于表、记录还是无效的。这个方法很简单:

```
@Override
public String getType(Uri uri) {

switch (uriMatcher.match(uri)) {
case TaskContract.TASKS_LIST:
return TaskContract.CONTENT_TYPE;

case TaskContract.TASKS_ITEM:
return TaskContract.CONTENT_ITEM_TYPE;

default:
throw new IllegalArgumentException("Invalid URI: "+uri);
}

}
```

## 使用您自己的提供商

为了使用我们新的内容提供商，我们需要打开`MainActivity.java`并修改它的一些代码。

转到`updateUI()`方法，我们将改变从数据库中查询数据的方法。删除这段代码:

```
helper = new TaskDBHelper(MainActivity.this);
SQLiteDatabase sqlDB = helper.getReadableDatabase();
Cursor cursor = sqlDB.query(TaskContract.TABLE,
new String[]{TaskContract.Columns._ID, TaskContract.Columns.TASK},
null, null, null, null, null);
```

并替换为:

```
Uri uri = TaskContract.CONTENT_URI;
Cursor cursor = this.getContentResolver().query(uri,null,null,null,null);
```

要删除任务，请从`onDoneButtonClick()`方法中删除以下代码:

```
String sql = String.format("DELETE FROM %s WHERE %s = '%s'",
TaskContract.TABLE,
TaskContract.Columns.TASK,
task);

helper = new TaskDBHelper(MainActivity.this);
SQLiteDatabase sqlDB = helper.getWritableDatabase();
sqlDB.execSQL(sql);
```

用这个替换它:

```
Uri uri = TaskContract.CONTENT_URI;
this.getContentResolver().delete(uri,
TaskContract.Columns.TASK + "=?",
new String[]{task});
```

现在，您可以使用创建的内容提供者删除任务，而不需要任何 SQL 查询。

最后，我们需要使用我们创建的`insert()`方法插入新任务。找到`onOptionsItemSelected()`方法并替换以下代码:

```
helper = new TaskDBHelper(MainActivity.this);
SQLiteDatabase db = helper.getWritableDatabase();
ContentValues values = new ContentValues();

values.clear();
values.put(TaskContract.Columns.TASK,task);

db.insertWithOnConflict(TaskContract.TABLE,null,values,SQLiteDatabase.CONFLICT_IGNORE);
```

使用:

```
helper = new TaskDBHelper(MainActivity.this);
SQLiteDatabase db = helper.getWritableDatabase();
ContentValues values = new ContentValues();

values.clear();
values.put(TaskContract.Columns.TASK,task);

Uri uri = TaskContract.CONTENT_URI;
getApplicationContext().getContentResolver().insert(uri,values);
```

现在，将使用我们之前创建的`insert()`方法将这些值添加到数据库中。

## 结论

如你所见，内容提供商并不辛苦，尤其是在几个项目中尝试之后。

我希望这篇指南能让你对 Android 内容提供商、他们的实现和使用有一个很好的了解。你可以在 [Github](https://github.com/sitepoint-examples/TodoList-ContentProviders) 上找到这个教程的源代码。

## 分享这篇文章