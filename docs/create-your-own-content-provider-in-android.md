# 在 Android 中创建自己的内容提供商

> 原文：<https://www.sitepoint.com/create-your-own-content-provider-in-android/>

Android 为不同的应用程序提供了多种方式在平台上相互通信。Android 应用程序可以与其他应用程序共享数据，其他应用程序可以使用这些数据来构建自己的逻辑。举个例子，你可能需要在电话上查询联系方式。在 Android 中，分享数据的推荐方式是通过内容提供商。内容提供者是特定内容的所有者，它提供定义良好的 API 来读取、插入、更新和删除数据。内容提供商可以在内部使用任何地方存储其数据，如本地文件、本地数据库或一些远程服务。在这篇文章中，我们将学习如何创建我们自己的内容提供商，并从另一个应用程序访问数据。

在 [GitHub](https://github.com/sitepoint-editors/AndroidContentProvider) 上找到最终项目。

## 创建内容提供者类

我们将首先在应用程序中创建一个内容提供者类，它可以保存图像元数据项。我们将使用本地 SQLite 数据库来存储数据，但是您可以使用任何您喜欢的地方来存储数据。

首先创建一个`ImagesProvider`类，扩展`ContentProvider`类并覆盖下面的方法。

```
import android.content.ContentProvider;
import android.content.ContentValues;
import android.database.Cursor;
import android.net.Uri;
public class ImagesProvider extends ContentProvider {

    @Override
    public String getType(Uri uri) {
        return "";
    }

    @Override
    public boolean onCreate() {
        return true;
    }

    @Override
    public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder) {
       return null;
    }

    @Override
    public Uri insert(Uri uri, ContentValues values) {
        return null;
    }

    @Override
    public int delete(Uri uri, String selection, String[] selectionArgs) {
        return 0;
    }

    @Override
    public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs) {
        return 0;
    }
}
```

在上面的代码中，我们创建了一个`ImagesProvider`类，它覆盖了创建内容提供者所必需的方法。

我们将从实现`getType`方法开始。`getType`方法将数据的 [Mime 类型](https://en.wikipedia.org/wiki/MIME)作为字符串返回。

返回的 mime 类型应该采用格式`vnd.<uri pattern>./vnd.<name>.<type>`。其中，单行的`<uri pattern>`应该是`android.cursor.item`，多行的`android.cursor.dir`和`<name>`应该是全局唯一的(使用包名)。`<type>`应该是对应的 URI 所独有的。现在让我们更新代码来实现`getType`,如下所示:

```
import android.content.ContentProvider;
import android.content.ContentValues;
import android.content.UriMatcher;
import android.database.Cursor;
import android.net.Uri;

public class ImagesProvider extends ContentProvider {

    private static final String PROVIDER_NAME = "androidcontentproviderdemo.androidcontentprovider.images";
    private static final Uri CONTENT_URI = Uri.parse("content://" + PROVIDER_NAME + "/images");
    private static final int IMAGES = 1;
    private static final int IMAGE_ID = 2;
    private static final UriMatcher uriMatcher = getUriMatcher();
    private static UriMatcher getUriMatcher() {
        UriMatcher uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
        uriMatcher.addURI(PROVIDER_NAME, "images", IMAGES);
        uriMatcher.addURI(PROVIDER_NAME, "images/#", IMAGE_ID);
        return uriMatcher;
    }

    @Override
    public String getType(Uri uri) {
        switch (uriMatcher.match(uri)) {
            case IMAGES:
                return "vnd.android.cursor.dir/vnd.com.androidcontentproviderdemo.androidcontentprovider.provider.images";
            case IMAGE_ID:
                return "vnd.android.cursor.item/vnd.com.androidcontentproviderdemo.androidcontentprovider.provider.images";

        }
        return "";
    }
    @Override
    public boolean onCreate() {
        return true;
    }

    @Override
    public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder) {
       return null;
    }

    @Override
    public Uri insert(Uri uri, ContentValues values) {
        return null;
    }

    @Override
    public int delete(Uri uri, String selection, String[] selectionArgs) {
        return 0;
    }

    @Override
    public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs) {
        return 0;
    }
}
```

## 实现 onCreate 和 query 函数

为了存储内容，我们需要一个用于内容提供者的数据库助手类。创建一个`ImageDataBase`类，如下所示

```
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.database.sqlite.SQLiteQueryBuilder;

public class ImageDataBase extends SQLiteOpenHelper {

    private static final String DATABASE_NAME = "ImagesDatabase.db";
    private static final String TABLE_NAME = "imagestore";
    private static final String SQL_CREATE = "CREATE TABLE " + TABLE_NAME +
            " (_id INTEGER PRIMARY KEY, IMAGETITLE TEXT , IMAGEURL TEXT , IMAGEDESC TEXT )";

    private static final String SQL_DROP = "DROP TABLE IS EXISTS " + TABLE_NAME ;

    ImageDataBase(Context context) {
        super(context, DATABASE_NAME, null, 1);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(SQL_CREATE);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL(SQL_DROP);
        onCreate(db);
    }

    public Cursor getImages(String id, String[] projection, String selection, String[] selectionArgs, String sortOrder) {
        SQLiteQueryBuilder sqliteQueryBuilder = new SQLiteQueryBuilder();
        sqliteQueryBuilder.setTables(TABLE_NAME);

        if(id != null) {
            sqliteQueryBuilder.appendWhere("_id" + " = " + id);
        }

        if(sortOrder == null || sortOrder == "") {
            sortOrder = "IMAGETITLE";
        }
        Cursor cursor = sqliteQueryBuilder.query(getReadableDatabase(),
                projection,
                selection,
                selectionArgs,
                null,
                null,
                sortOrder);
        return cursor;
    }
}
```

上面的类扩展了`SQLiteOpenHelper`并在“ImagesDatabase.db”数据库中创建了一个名为“imagestore”的表，该表包含列“id”、“IMAGETITLE”、“IMAGEURL”和“IMAGEDESC”。`getImages`函数使用`SQLiteQueryBuilder`类查询这个数据库，并返回结果光标。现在让我们在内容提供者中实现`onCreate`和`query`功能。

```
 import android.content.ContentProvider;
import android.content.ContentValues;
import android.content.Context;
import android.content.UriMatcher;
import android.database.Cursor;
import android.net.Uri;

public class ImagesProvider extends ContentProvider {

    private static final String PROVIDER_NAME = "androidcontentproviderdemo.androidcontentprovider.images";
    private static final Uri CONTENT_URI = Uri.parse("content://" + PROVIDER_NAME + "/images");
    private static final int IMAGES = 1;
    private static final int IMAGE_ID = 2;
    private static final UriMatcher uriMatcher = getUriMatcher();
    private static UriMatcher getUriMatcher() {
        UriMatcher uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
        uriMatcher.addURI(PROVIDER_NAME, "images", IMAGES);
        uriMatcher.addURI(PROVIDER_NAME, "images/#", IMAGE_ID);
        return uriMatcher;
    }

    private ImageDatabase imageDataBase = null;

    @Override
    public String getType(Uri uri) {
        switch (uriMatcher.match(uri)) {
            case IMAGES:
                return "vnd.android.cursor.dir/vnd.com.androidcontentproviderdemo.androidcontentprovider.provider.images";
            case IMAGE_ID:
                return "vnd.android.cursor.item/vnd.com.androidcontentproviderdemo.androidcontentprovider.provider.images";

        }
        return "";
    }

    @Override
    public boolean onCreate() {
        Context context = getContext();
        imageDataBase = new ImageDataBase(context);
        return true;
    }

    @Override
    public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder) {
        String id = null;
        if(uriMatcher.match(uri) == IMAGE_ID) {
            //Query is for one single image. Get the ID from the URI.
            id = uri.getPathSegments().get(1);
        }
        return imageDataBase.getImages(id, projection, selection, selectionArgs, sortOrder);
    }

    @Override
    public Uri insert(Uri uri, ContentValues values) {
        return null;
    }

    @Override
    public int delete(Uri uri, String selection, String[] selectionArgs) {
        return 0;
    }

    @Override
    public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs) {
        return 0;
    }
}
```

在`onCreate`函数中，我们创建了一个`ImageDatabase`类的对象，并将上下文传递给它。我们存储这个变量，以便我们可以在内容提供者的各种操作中使用它。在`query`函数中，我们检查该查询是否针对一个`id`。在这种情况下，我们获取`id`，如果没有，我们将其保存为`null`，并将这些值传递给`ImageDatabase:getImages`以获取图像数据并返回结果光标。

## 在内容提供商中插入、删除和更新数据

一旦“查询”功能完成，我们将在内容提供者中实现其他功能。对于`insert`、`delete`和`update`，我们需要在`ImageDatabase`类中添加一个支持函数，如下所示

```
import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.database.sqlite.SQLiteQueryBuilder;

import java.sql.SQLException;

public class ImageDataBase extends SQLiteOpenHelper {

    private static final String DATABASE_NAME = "ImagesDatabase.db";
    private static final String TABLE_NAME = "imagestore";
    private static final String SQL_CREATE = "CREATE TABLE " + TABLE_NAME +
            " (_id INTEGER PRIMARY KEY, IMAGETITLE TEXT , IMAGEURL TEXT , IMAGEDESC TEXT )";

    private static final String SQL_DROP = "DROP TABLE IS EXISTS " + TABLE_NAME ;

    ImageDataBase(Context context) {
        super(context, DATABASE_NAME, null, 1);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(SQL_CREATE);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL(SQL_DROP);
        onCreate(db);
    }

    public Cursor getImages(String id, String[] projection, String selection, String[] selectionArgs, String sortOrder) {
        SQLiteQueryBuilder sqliteQueryBuilder = new SQLiteQueryBuilder();
        sqliteQueryBuilder.setTables(TABLE_NAME);

        if(id != null) {
            sqliteQueryBuilder.appendWhere("_id" + " = " + id);
        }

        if(sortOrder == null || sortOrder == "") {
            sortOrder = "IMAGETITLE";
        }
        Cursor cursor = sqliteQueryBuilder.query(getReadableDatabase(),
                projection,
                selection,
                selectionArgs,
                null,
                null,
                sortOrder);
        return cursor;
    }

    public long addNewImage(ContentValues values) throws SQLException {
        long id = getWritableDatabase().insert(TABLE_NAME, "", values);
        if(id <=0 ) {
            throw new SQLException("Failed to add an image");
        }

        return id;
    }

    public int deleteImages(String id) {
        if(id == null) {
            return getWritableDatabase().delete(TABLE_NAME, null , null);
        } else {
            return getWritableDatabase().delete(TABLE_NAME, "_id=?", new String[]{id});
        }
    }

    public int updateImages(String id, ContentValues values) {
        if(id == null) {
            return getWritableDatabase().update(TABLE_NAME, values, null, null);
        } else {
            return getWritableDatabase().update(TABLE_NAME, values, "_id=?", new String[]{id});
        }
    }
}
```

所有新函数都访问数据库，并通过传递 id 和内容值分别调用`insert`、`delete`和`update`函数。现在我们更新`ImagesProvider`类如下

```
import android.content.ContentProvider;
import android.content.ContentUris;
import android.content.ContentValues;
import android.content.Context;
import android.content.UriMatcher;
import android.database.Cursor;
import android.net.Uri;
import android.util.Log;

public class ImagesProvider extends ContentProvider {

    private static final String PROVIDER_NAME = "androidcontentproviderdemo.androidcontentprovider.images";
    private static final Uri CONTENT_URI = Uri.parse("content://" + PROVIDER_NAME + "/images");
    private static final int IMAGES = 1;
    private static final int IMAGE_ID = 2;
    private static final UriMatcher uriMatcher = getUriMatcher();
    private static UriMatcher getUriMatcher() {
        UriMatcher uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
        uriMatcher.addURI(PROVIDER_NAME, "images", IMAGES);
        uriMatcher.addURI(PROVIDER_NAME, "images/#", IMAGE_ID);
        return uriMatcher;
    }

    private ImageDatabase imageDataBase = null;

    @Override
    public String getType(Uri uri) {
        switch (uriMatcher.match(uri)) {
            case IMAGES:
                return "vnd.android.cursor.dir/vnd.com.androidcontentproviderdemo.androidcontentprovider.provider.images";
            case IMAGE_ID:
                return "vnd.android.cursor.item/vnd.com.androidcontentproviderdemo.androidcontentprovider.provider.images";

        }
        return "";
    }

    @Override
    public boolean onCreate() {
        Context context = getContext();
        imageDataBase = new ImageDatabase(context);
        return true;
    }

    @Override
    public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder) {
        String id = null;
        if(uriMatcher.match(uri) == IMAGE_ID) {
            //Query is for one single image. Get the ID from the URI.
            id = uri.getPathSegments().get(1);
        }
        return imageDataBase.getImages(id, projection, selection, selectionArgs, sortOrder);
    }

    @Override
    public Uri insert(Uri uri, ContentValues values) {
        try {
            long id = imageDataBase.addNewImage(values);
            Uri returnUri = ContentUris.withAppendedId(CONTENT_URI, id);
            return returnUri;
        } catch(Exception e) {
            return null;
        }
    }

    @Override
    public int delete(Uri uri, String selection, String[] selectionArgs) {
        String id = null;
        if(uriMatcher.match(uri) == IMAGE_ID) {
            //Delete is for one single image. Get the ID from the URI.
            id = uri.getPathSegments().get(1);
        }

        return imageDataBase.deleteImages(id);
    }

    @Override
    public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs) {
        String id = null;
        if(uriMatcher.match(uri) == IMAGE_ID) {
            //Update is for one single image. Get the ID from the URI.
            id = uri.getPathSegments().get(1);
        }

        return imageDataBase.updateImages(id, values);
    }
}
```

在上面的代码中，如果 URI 是一个简单的图像，我们得到 id 值，否则我们保持 id 为空。然后将该值传递给`ImageDataBase`类中相应的函数。

## 在 AndroidManifest 中声明您的内容提供者

我们需要使用`provide`标签在 *AndroidManifest.xml* 文件中声明我们的内容提供者。

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.androidcontentproviderdemo.androidcontentprovider">
    <application android:allowBackup="true" android:label="@string/app_name"
        android:icon="@mipmap/ic_launcher" android:theme="@style/AppTheme">
        <provider android:name="com.androidcontentproviderdemo.androidcontentprovider.ImagesProvider" android:authorities="androidcontentproviderdemo.androidcontentprovider.images">
        </provider>
    </application>
</manifest>
```

在上面的`<provider>`标签中，`android:name`属性应该是内容提供者类，`android:authorities`应该是标识内容的 URI。

## 使用内容提供商

一旦以上完成，我们就可以测试我们的内容提供者了。为此，我们将在当前的主活动中创建一个测试应用程序，再次为您的应用程序名称更改适当的值。

```
import android.app.Activity;
import android.content.ContentValues;
import android.content.CursorLoader;
import android.database.Cursor;
import android.net.Uri;
import android.os.Bundle;
import android.util.Log;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.EditText;
import android.widget.ListView;
import android.widget.SimpleCursorAdapter;
import android.widget.Toast;

public class MainActivity extends Activity {

    private static final String PROVIDER_NAME = "androidcontentproviderdemo.androidcontentprovider.images";
    private static final Uri CONTENT_URI = Uri.parse("content://" + PROVIDER_NAME + "/images");

    private ListView listView;
    private SimpleCursorAdapter adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        listView = (ListView) findViewById(R.id.lstViewImages);

        adapter = new SimpleCursorAdapter(getBaseContext(),
                R.layout.list_layout,
                null,
                new String[] { "IMAGETITLE", "IMAGEURL", "IMAGEDESC"},
                new int[] { R.id.imgTitle , R.id.imgUrl, R.id.imgDesc }, 0);

        listView.setAdapter(adapter);
        refreshValuesFromContentProvider();
    }

    private void refreshValuesFromContentProvider() {
        CursorLoader cursorLoader = new CursorLoader(getBaseContext(), CONTENT_URI,
                null, null, null, null);
        Cursor c = cursorLoader.loadInBackground();
        adapter.swapCursor(c);
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        // Inflate the menu; this adds items to the action bar if it is present.
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

    public void onClickAddImage(View view) {
        ContentValues contentValues = new ContentValues();
        contentValues.put("IMAGETITLE", ((EditText) findViewById(R.id.edtTxtImageTitle)).getText().toString());
        contentValues.put("IMAGEURL" , ((EditText)findViewById(R.id.edtImageUrl)).getText().toString());
        contentValues.put("IMAGEDESC", ((EditText) findViewById(R.id.edtImageDesc)).getText().toString());
        Uri uri = getContentResolver().insert(CONTENT_URI, contentValues);
        Toast.makeText(getBaseContext(), uri.toString(), Toast.LENGTH_LONG).show();
        refreshValuesFromContentProvider();
    }
}
```

此活动涉及两种布局，一种用于主内容视图，另一种用于列表视图，如下所示:

*activitymain.xml*

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin"
    tools:context=".ContentProviderUsageActivity">

    <LinearLayout
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_centerHorizontal="true">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?android:attr/textAppearanceMedium"
            android:text="Image Title"
            android:id="@+id/txtViewImageTitle"
            android:layout_gravity="center_horizontal" />

        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/edtTxtImageTitle"
            android:layout_gravity="center_horizontal" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?android:attr/textAppearanceMedium"
            android:text="Image URL"
            android:id="@+id/txtViewImageUrl"
            android:layout_gravity="center_horizontal" />

        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/edtImageUrl"
            android:layout_gravity="center_horizontal" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?android:attr/textAppearanceMedium"
            android:text="Image description"
            android:id="@+id/txtViewImageDesc"
            android:layout_gravity="center_horizontal" />

        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/edtImageDesc"
            android:layout_gravity="center_horizontal" />

        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Add Image"
            android:id="@+id/btnAddImage"
            android:layout_gravity="center_horizontal"
            android:onClick="onClickAddImage" />

        <ListView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/lstViewImages"
            android:layout_gravity="center_horizontal" />
    </LinearLayout>

</RelativeLayout>
```

*列表布局. xml*

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/imgTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        />

    <TextView
        android:id="@+id/imgUrl"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        />

    <TextView
        android:id="@+id/imgDesc"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        />

</LinearLayout>
```

在上面的代码中，`onCreate`函数使用`CursorLoader`函数从内容提供者获取所有内容，并将`CONTENT_URI`传递给它。

为了插入值，调用`getContentResolver().insert`，传递内容 URI 和要插入的`ContentValues`。如果我们运行这个活动，它将如下所示

![](img/5aa600bbd9e9999ca4608156b9648d1b.png)

## 结论

Android 中的内容提供商为应用程序提供了一种干净、系统的方式来共享和使用来自其他应用程序的数据。它规范了多个应用程序共享和使用内容的方式。Android 中存在许多内置的内容提供者，API 使得编写自己的自定义内容提供者或使用自定义内容提供者变得很容易。

祝您创建下一个内容提供商愉快，如果您有任何反馈，请告诉我。

## 分享这篇文章