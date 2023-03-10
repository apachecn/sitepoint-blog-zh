# 在 Android 中处理和显示图像

> 原文：<https://www.sitepoint.com/handling-displaying-images-android/>

Android 提供了许多视图，我们可以用它们来定义应用程序的用户界面。其中，它提供了大量的显示信息和用户输入，包括文本和图像视图。

Android 提供了视图，可以用来显示来自不同来源的图像，并提供它们之间的转换。其中一些视图是`ImageView`和`ImageSwitcher`。这些视图提供了在用户界面中显示图像的高级功能，以便我们可以专注于我们想要显示的图像，而不是关注渲染。

在这篇文章中，我们将看到如何在用户界面中有效地使用这些视图。

## 使用图像视图显示图像。

为了渲染图像，Android 为我们提供了`ImageView`类。让我们首先创建一个程序，该程序将使用一个`ImageView`来显示一些图像和一个按钮，单击该按钮将改变`ImageView`中的图像。你可以在 GitHub 上找到[这部分的代码。](https://github.com/sitepoint-editors/ImageGallery)

创建一个基本的 Android 项目，该项目包含一个从布局文件设置主视图的活动，不做任何其他事情。然后打开布局文件，添加`ImageView`和一个按钮，如下所示:

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingLeft="16dp"
    android:paddingRight="16dp"
    android:orientation="vertical">

    <ImageView
        android:id="@+id/imageDisplay"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <Button
        android:id="@+id/btnRotateImage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Next Image" />
</LinearLayout>
```

在上面的代码中，我们创建了一个`LinearLayout`并添加了一个`ImageView`来显示一张图片和一个按钮来旋转`imageView`中的图片。

根据您计划支持的屏幕尺寸，向资源文件夹添加一些图像，如下所示:

![Image Resources](img/ccddc2463636b0054039202b40cb7394.png)

现在，按如下方式更新您的活动代码，为您的项目使用适当的名称:

```
public class ImageChangingActivity extends Activity {

    private Integer images[] = {R.drawable.pic1, R.drawable.pic2, R.drawable.pic3};
    private int currImage = 0;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_image_changing);

        setInitialImage();
        setImageRotateListener();
    }

    private void setImageRotateListener() {
        final Button rotatebutton = (Button) findViewById(R.id.btnRotateImage);
        rotatebutton.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View arg0) {
                currImage++;
                if (currImage == 3) {
                    currImage = 0;
                }
                setCurrentImage();
            }
        });
    }

    private void setInitialImage() {
        setCurrentImage();
    }

    private void setCurrentImage() {

        final ImageView imageView = (ImageView) findViewById(R.id.imageDisplay);
        imageView.setImageResource(images[currImage]);

    }
}
```

上面，我们为存储在 resources 文件夹中的图像创建了一个资源 id 数组。在`OnCreate`方法中，我们将内容视图设置为创建的布局。在`setImageRotateListener`函数中，我们为按钮的`onClick`事件设置了一个监听器，该事件改变了`currentImage`计数器并在`ImageView`中设置了新图像。

`setCurrentImage`函数使用`findViewById`函数获取`ImageView`对象，然后使用`ImageView`上的`setImageResource`函数设置当前图像的资源 id，这将在图像视图中显示图像。

如果您运行该程序，您应该会在图像视图中看到该图像，单击它应该会将图像更改为下一个图像:

![App running](img/83dceeb8be8d5e6587489e2e7868f82b.png)

## 在 Android 中使用图像切换器视图。

在上面的例子中，我们在图像视图中切换了图像。这种图像切换不是以非常平滑的方式发生的，当图像改变时，您可能想要使用过渡。为此，我们使用`ImageSwitcher`视图。

在 GitHub 上找到这个例子的[代码。](https://github.com/sitepoint-editors/ImageSwitcherExample)

首先将图像切换器视图添加到布局中，如下所示:

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingLeft="16dp"
    android:paddingRight="16dp"
    android:orientation="vertical">

    <ImageSwitcher
        android:id="@+id/imageSwitcher"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <Button
        android:id="@+id/btnRotateImage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Next Image" />
</LinearLayout>
```

然后将下面的代码添加到我们的 Activity 中，它将初始化`ImageSwitcher`并设置按钮来改变带有过渡的图像。

```
public class ImageSwitcherExampleActivity extends Activity {

    private Integer images[] = {R.drawable.pic1, R.drawable.pic2, R.drawable.pic3};
    private int currImage = 0;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_image_switcher_example);

        initializeImageSwitcher();
        setInitialImage();
        setImageRotateListener();
    }

    private void initializeImageSwitcher() {
        final ImageSwitcher imageSwitcher = (ImageSwitcher) findViewById(R.id.imageSwitcher);
        imageSwitcher.setFactory(new ViewFactory() {
            @Override
            public View makeView() {
                ImageView imageView = new ImageView(ImageSwitcherExampleActivity.this);
                return imageView;
            }
        });

        imageSwitcher.setInAnimation(AnimationUtils.loadAnimation(this, android.R.anim.slide_in_left));
        imageSwitcher.setOutAnimation(AnimationUtils.loadAnimation(this, android.R.anim.slide_out_right));
    }

    private void setImageRotateListener() {
        final Button rotatebutton = (Button) findViewById(R.id.btnRotateImage);
        rotatebutton.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View arg0) {
                currImage++;
                if (currImage == 3) {
                    currImage = 0;
                }
                setCurrentImage();
            }
        });
    }

    private void setInitialImage() {
        setCurrentImage();
    }

    private void setCurrentImage() {
        final ImageSwitcher imageSwitcher = (ImageSwitcher) findViewById(R.id.imageSwitcher);
        imageSwitcher.setImageResource(images[currImage]);
    }
}
```

在上面的代码中，我们获取了`ImageSwitcher`对象，然后设置了`ViewFactory`，这创建了一个普通的`ImageView`。然后我们设置动画淡入淡出。我们更新`setCurrentImage`函数来设置 ImageSwitcher 中的图像。

现在图像会随着动画而改变。

![ImageSwitcher Example](img/599e1419c1406779363a4854941bf6b2.png)

## 从互联网下载和设置 ImageView 上的图像。

[这部分的代码可以在 GitHub](https://github.com/sitepoint-editors/ImageDownloader) 上找到。

您可能并不总是有本地可用的图像，而是希望从互联网上显示它们。

您不应该在 UI 线程中进行任何网络操作，而是应该在不同的后台线程中进行下载。我们将在一个`Async`任务中完成这项工作。首先在我们的`AndroidManifest.xml`中设置使用互联网的权限，如下所示:

```
<uses-permission android:name="android.permission.INTERNET" />
```

您的项目布局应该有一个`ImageView`和一个`Button`，如下所示:

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingLeft="16dp"
    android:paddingRight="16dp"
    android:orientation="vertical">

    <ImageView
        android:id="@+id/imageDisplay"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <Button
        android:id="@+id/btnRotateImage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Next Image" />
</LinearLayout>
```

然后我们创建一个`AsyncTask`，它接受`ImageView`和要下载的 URL，下载图像并将图像设置在`ImageView`中。

`ImageDownloader`异步任务下载图像数据并设置`ImageView`。完整的活动代码现在应该如下所示:

```
public class ImagedownloaderActivity extends Activity {

    private String imageUrls[] = {"http://www.abc.com/image1.jpg",
            "http://www.abc.com/image2.jpg",
            "http://www.abc.com/image3.jpg"
    };

    private class ImageDownloader extends AsyncTask<String, Void, Bitmap> {
        ImageView bmImage;

        public ImageDownloader(ImageView bmImage) {
            this.bmImage = bmImage;
        }

        protected Bitmap doInBackground(String... urls) {
            String url = urls[0];
            Bitmap bitmap = null;
            try {
                InputStream in = new java.net.URL(url).openStream();
                bitmap = BitmapFactory.decodeStream(in);
            } catch (Exception e) {
                Log.e("MyApp", e.getMessage());
            }
            return bitmap;
        }

        protected void onPostExecute(Bitmap result) {
            bmImage.setImageBitmap(result);
        }
    }

    private int currImage = 0;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_imagedownloader);

        setInitialImage();
        setImageRotateListener();
    }

    private void setImageRotateListener() {
        final Button rotatebutton = (Button) findViewById(R.id.btnRotateImage);
        rotatebutton.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View arg0) {
                currImage++;
                if (currImage == 3) {
                    currImage = 0;
                }
                setCurrentImage();
            }
        });
    }

    private void setInitialImage() {
        setCurrentImage();
    }

    private void setCurrentImage() {
        final ImageView imageView = (ImageView) findViewById(R.id.imageDisplay);
        ImageDownloader imageDownLoader = new ImageDownloader(imageView);
        imageDownLoader.execute(imageUrls[currImage]);
    }
}
```

在上面的代码中，我们将图像的 URL 存储在`imageUrls`数组中。在`setCurrentImage`函数中，我们将`ImageView`传递给`ImageDownloader`异步任务，并传递图片的 URL 以下载并设置在`ImageView`中。`ImageDownloader`异步任务将下载图像并将其设置在`ImageView`中。

## 使用水平滚动视图实现图库。

这个例子的代码可以在 GitHub 上找到。

在上面的例子中，我们看到了如何使用`ImageView`一次显示一幅图像。有时我们可能希望显示可变数量的图像，并让用户滚动浏览它们。这可以通过将一个`LinearLayout`放入一个水平的`scrollView`中，然后动态地将`ImageViews`添加到这个线性布局中来实现。为此，我们创建一个名为`ImageGalleryActivity`的新活动，并相应地更新布局文件:

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.example.imagegallery.ImageGalleryActivity">

    <HorizontalScrollView
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <LinearLayout
            android:id="@+id/imageGallery"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:orientation="horizontal" />
    </HorizontalScrollView>
</RelativeLayout>
```

活动的代码:

```
public class ImageGalleryActivity extends Activity {

    private Integer images[] = {R.drawable.pic1, R.drawable.pic2, R.drawable.pic3};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_image_gallery);
        addImagesToThegallery();
    }

    private void addImagesToThegallery() {
        LinearLayout imageGallery = (LinearLayout) findViewById(R.id.imageGallery);
        for (Integer image : images) {
            imageGallery.addView(getImageView(image));
        }
    }

    private View getImageView(Integer image) {
        ImageView imageView = new ImageView(getApplicationContext());
        LinearLayout.LayoutParams lp = new LinearLayout.LayoutParams(LinearLayout.LayoutParams.WRAP_CONTENT, LinearLayout.LayoutParams.WRAP_CONTENT);
        lp.setMargins(0, 0, 10, 0);
        imageView.setLayoutParams(lp);
        imageView.setImageResource(image);
        return imageView;
    }
}
```

在上面的代码中，我们动态地创建了`ImageViews`并给它们添加了边距。`LinerLayout`的方向设置为水平。现在，如果我们运行该程序，我们将能够在一个水平滚动的图库中看到这些图像，如下所示。

![Horizontal Gallery](img/14dcbb479a998b03fa859ce5edf0ec3e.png)

## 结论

图像是现代移动界面的重要组成部分，希望本教程已经向您展示了 Android 提供的一些工具来简化这一过程。你还在等什么？看到了吗？

## 分享这篇文章