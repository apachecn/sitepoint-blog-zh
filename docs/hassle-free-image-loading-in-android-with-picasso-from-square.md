# 使用 Square 中的 Picasso 在 Android 中轻松加载图像

> 原文：<https://www.sitepoint.com/hassle-free-image-loading-in-android-with-picasso-from-square/>

图像为你的应用界面增添了味道、个性和乐趣。Android 中的图像加载通常包括添加多行代码样板代码。[来自](http://square.github.io/picasso/)[广场](http://square.github.io)的 Picasso 是使用起来最简单的库之一，包括内存和数据优化，允许你选择在下载图像后不将其保存在缓存中。

在本教程中，我将展示如何从外部位置下载图像，使用*文件路径*、*图像资源 id* 或使用 [Picasso](http://square.github.io/picasso/) 的 Android 应用程序中的任何 *URI* 。

你可以在 [GitHub](https://github.com/sitepoint-editors/picasso) 上找到这个项目。您可以在这里下载[项目中使用的 drawables，确保您将它们添加到 *drawables* 文件夹中。](https://github.com/sitepoint-editors/picasso/tree/master/app/src/main/res/drawable)

![Sample application screenshot](img/2662ec72458c93fc4974d7638823833a.png)

## 将毕加索添加到您的项目中

**注**:我用的是毕加索 2.5.2 版本。

在 *Android Studio* 中新建一个*项目*，并在 *build.gradle (Module: app)* 中的`dependencies`中添加这一行:

```
compile 'com.squareup.picasso:picasso:2.5.2' 
```

同步项目中的 Gradle 以下载库。

在 *AndroidManifest.xml* 中添加互联网权限。

```
<uses-permission android:name="android.permission.INTERNET"/> 
```

在这个项目中，我将使用一个单一的活动类和布局资源文件。

## XML 布局

这个示例应用程序的界面包括三个`ImageView`，在一个垂直方向的线性布局中。每个图像将被下载，保存和显示使用不同的方法的图书馆。

`activity_main.xml`的代码如下(别忘了改成你的包名):

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.example.theodhor.picasso.MainActivity">

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:id="@+id/activity_main"
        android:layout_centerHorizontal="true">

        <ImageView
            android:layout_width="150dp"
            android:layout_height="150dp"
            android:layout_gravity="center_horizontal"
            android:id="@+id/image_1"
            android:layout_margin="5dp"/>

        <ImageView
            android:layout_width="150dp"
            android:layout_height="150dp"
            android:layout_gravity="center_horizontal"
            android:id="@+id/image_2"
            android:layout_margin="5dp"/>

        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:id="@+id/image_3"
            android:layout_margin="5dp"/>

    </LinearLayout>

</RelativeLayout> 
```

## 显示图像

第一步是在 Java 代码中为上面的每个`ImageViews`创建一个引用。在`MainActivity`类声明下面添加以下内容:

```
ImageView firstImage, secondImage, thirdImage; 
```

在这段代码下面，创建`String`变量，并赋予它们任意图像 URI 的值。因为您使用了三个`ImageView`，所以每个都需要一个 URI:

```
private static final String IMAGE_1 =
"http://uxmastery.com/wp-content/uploads/2015/06/sitepoint-logo-195x195.jpg";
private static final String IMAGE_2 =
"http://uxmastery.com/wp-content/uploads/2015/06/sitepoint-logo-195x195.jpg";
private static final String IMAGE_3 =
"https://www.sitepoint.com/premium/books/git-fundamentals/onliimg/sitepoint-logo-2012.png"; 
```

在`onCreate()`方法中，为`ImageViews`创建引用:

```
firstImage = (ImageView)findViewById(R.id.image_1);
secondImage = (ImageView)findViewById(R.id.image_2);
thirdImage = (ImageView)findViewById(R.id.image_3); 
```

下载图像、将图像保存在设备内存中并显示图像的最简单方法是使用这行代码，该代码添加在图像引用的下面:

```
Picasso.with(this).load(IMAGE_1).centerCrop().fit().into(firstImage); 
```

您需要使用`with(context)`在应用程序上下文中引用 Picasso 的每个实例。那么`.load(parameter)`可以接受任意图像的一个`file`、`resource id`、`path`或`uri`作为参数。`.centerCrop()`将图像放置在`ImageView`的中心，并使用`.fit()`根据`ImageView`的大小拉伸图像。您也可以使用`.resize(int width, int height)`来设置下载图像的明确大小。最后一个方法`.into(parameter)`接受`ImageView`来显示图像作为参数。

这是显示外部来源图像的最短代码行。

这是加载图像的最短代码行，但 Picasso 允许您使用不同的方法来更好地使用网络数据、设备内存和处理器。

一个更加定制化的例子是:

```
Picasso.with(this)
  .load(IMAGE_1)
  .centerCrop()
  .fit()                    .placeholder(getResources().getDrawable(R.drawable.placeholder,null))
  .error(getResources().getDrawable(R.drawable.no_image,null))
  .rotate(45)
  .memoryPolicy(MemoryPolicy.NO_CACHE)
  .priority(Picasso.Priority.HIGH)
  .into(firstImage); 
```

对于较大的图像，您可以使用一个`placeholder`来显示一个虚拟图像，直到所需的图像已经下载并准备好显示。当加载遇到错误时，您可以使用相同的技术来避免空的`ImageView`。

通过跳过`Cache`，Picasso 在处理请求时避免了缓存查找。当您确定在缓存中找不到图像时，这可能适用。下载后，使用`.memoryPolicy(MemoryPolicy.NO_STORE)`不将图像存储在缓存中。

每个图像可以有自己的`Priority` : `LOW`，`NORMAL`或`HIGH`。

您将使用一个`Target`来显示`secondImage`，它有三个主要方法来确定图像下载过程的状态。在前一个 Picasso 调用之后添加此代码:

```
Target target = new Target() {
   @Override
   public void onBitmapLoaded(Bitmap bitmap, Picasso.LoadedFrom from) {
          secondImage.setImageBitmap(bitmap);
   }

   @Override
   public void onBitmapFailed(Drawable errorDrawable) {
          secondImage.setImageDrawable(errorDrawable);
   }

   @Override
   public void onPrepareLoad(Drawable placeHolderDrawable) {                secondImage.setImageDrawable(getResources().getDrawable(R.drawable.prepare_load,null));
   }
};    

target.onBitmapFailed(getResources().getDrawable(R.drawable.no_image,null));

Picasso.with(this).load(IMAGE_2).priority(Picasso.Priority.HIGH).into(target); 
```

被覆盖的`Target`方法允许您更好地管理流程。您可以通过从`target`外部调用这些方法来设置它们的参数。您可以使用以下选项设置下载失败时显示的`bitmap`:

```
target.onBitmapFailed(getResources().getDrawable(R.drawable.no_image,null)); 
```

下一行创建 Picasso 的实例，下载位于 *IMAGE_2* 的图像。

您将使用“Picasso Builder”下载第三幅图像。这将有助于区分这个图像来自哪里。如果在设备内存或磁盘中找到图像，毕加索不会下载它，而是使用当前可用的图像。通过创建一个生成器，您可以显示显示图像源的*调试指示器*。

![Picasso source debug indicators](img/9254d8f74530ca225b20d4159275ccae.png)

构建器支持另一种方法，在图像加载失败时调用。

```
//Third method
Picasso.Builder picassoBuilder = new Picasso.Builder(this);
picassoBuilder.listener(new Picasso.Listener() {
    @Override
    public void onImageLoadFailed(Picasso picasso, Uri uri, Exception exception) {
        Log.e("TAG","Failed");
    }
});
Picasso picasso = picassoBuilder.build();
picasso.setLoggingEnabled(true);
picasso.setIndicatorsEnabled(true);
picasso.load(IMAGE_3).centerCrop().resize(400,100).placeholder(R.drawable.no_image).into(thirdImage); 
```

这次您将使用构建器创建毕加索的实例。由于此图像较宽，您需要调整图像大小以保持正确的比例。占位符可能是应用程序用户界面的一个很好的元素，因为我们永远无法预测下载每个图像所需的连接速度和时间。

## 结论

在本教程中，我向您展示了 Picasso library 的配置和使用有多简单，只需几行代码就可以下载、显示和格式化图像。

我希望你和我一样觉得它很容易使用，如果你有任何意见或问题，请在下面告诉我。

## 分享这篇文章