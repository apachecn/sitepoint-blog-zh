# Android 手势和触摸机制

> 原文：<https://www.sitepoint.com/android-gestures-and-touch-mechanics/>

我想我们都同意，移动技术已经成为技术时代的重要组成部分，现在是开发人员的主要关注点之一。mobile 成功的原因之一是能够通过手势和辅助功能与用户进行交互。移动技术为术语<q>用户互动</q>设定了新的限制。

用户以各种不同的方式与智能手机交互，从简单的触摸机制到拖动视图和多手指手势。这是一套完整的主要[移动手势](https://www.google.com/design/spec/patterns/gestures.html)示例。

![Mobile Gestures](img/a450e84c3fa8b0fbc4372c11d331c926.png)

在这篇文章中，我将介绍一些最重要的 Android 用户交互和手势，并实现自定义的 Android 手势和触摸事件。

为了制作一个完全交互式的应用程序，开发人员需要的不仅仅是`onClick`所以 Android SDK 支持各种手势检测。这些允许开发者设计用户与应用程序交互的方式。Android 提供了用于检测基本动作事件的`GestureDetector`类和用于处理更复杂手势的`MotionEvent`。

## 1\. GestureDetector

GestureDetector 类接收对应于一组特定用户手势的动作事件。比如上下轻敲、纵横轻扫(fling)、长短按、双击和滚动。GestureDetector 对于这些标准的用户交互非常强大，并且很容易在 Android UI 元素上设置`SimpleOnGestureListener` s。

这个类的简单之处在于覆盖了您需要的来自`GestureListener`的方法，并实现了所需的手势功能，而不必手动确定用户执行了什么手势。

### 检测常见手势

下面的例子是`GestureDetector`类常用手势的例子。首先，在活动类中声明一个`GestureDetector`实例。

```
 private GestureDetector mGestureDetector;
```

在 Activity 类中创建一个 java 类，实现`GestureDetector.OnGestureListener`和`GestureDetector.OnDoubleTapListener`接口来检测基本的 Android 手势。

```
 class Android_Gesture_Detector implements GestureDetector.OnGestureListener,
            GestureDetector.OnDoubleTapListener {

       @Override
        public boolean onDown(MotionEvent e) {
            Log.d("Gesture ", " onDown");
            return true;
        }

    @Override
        public boolean onSingleTapConfirmed(MotionEvent e) {
            Log.d("Gesture ", " onSingleTapConfirmed");
            return true;
        }

    @Override
        public boolean onSingleTapUp(MotionEvent e) {
            Log.d("Gesture ", " onSingleTapUp");
            return true;
        }

        @Override
        public void onShowPress(MotionEvent e) {
            Log.d("Gesture ", " onShowPress");
        }

    @Override
        public boolean onDoubleTap(MotionEvent e) {
            Log.d("Gesture ", " onDoubleTap");
         return true;
        }

        @Override
        public boolean onDoubleTapEvent(MotionEvent e) {
            Log.d("Gesture ", " onDoubleTapEvent");
            return true;
        }

        @Override
        public void onLongPress(MotionEvent e) {
            Log.d("Gesture ", " onLongPress");
        }

    @Override
    public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY) {

            Log.d("Gesture ", " onScroll");
            if (e1.getY() < e2.getY()){
                Log.d("Gesture ", " Scroll Down");
            }
          if(e1.getY() > e2.getY()){
                Log.d("Gesture ", " Scroll Up");
            }
            return true;
    }

        @Override
        public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY) {
    if (e1.getX() < e2.getX()) {
              Log.d("Gesture ", "Left to Right swipe: "+ e1.getX() + " - " + e2.getX());
               Log.d("Speed ", String.valueOf(velocityX) + " pixels/second");
     }
            if (e1.getX() > e2.getX()) {
              Log.d("Gesture ", "Right to Left swipe: "+ e1.getX() + " - " + e2.getX());
   Log.d("Speed ", String.valueOf(velocityX) + " pixels/second");
            }
            if (e1.getY() < e2.getY()) {
            Log.d("Gesture ", "Up to Down swipe: " + e1.getX() + " - " + e2.getX());
              Log.d("Speed ", String.valueOf(velocityY) + " pixels/second");
            }
            if (e1.getY() > e2.getY()) {
              Log.d("Gesture ", "Down to Up swipe: " + e1.getX() + " - " + e2.getX());
   Log.d("Speed ", String.valueOf(velocityY) + " pixels/second");
            }
            return true;

       }
}
```

`Android_Gesture_Detector`类将截取其中实现的所有基本手势，但是为了使用这些手势，我重写了活动的`onTouchEvent()`方法来截取这些触摸事件，并将它们重新路由到`Android_Gesture_Detector`类。

```
 @Override
public boolean onTouchEvent(MotionEvent event) {
    mGestureDetector.onTouchEvent(event);

    return super.onTouchEvent(event);
    // Return true if you have consumed the event, false if you haven't.
    // The default implementation always returns false.
}
```

让我们启动手势探测器。在`onCreate()`方法中添加以下内容，Android 手势检测器就可以检测用户交互了。

```
 // Create an object of the Android_Gesture_Detector  Class
Android_Gesture_Detector  android_gesture_detector  =  new Android_Gesture_Detector();
// Create a GestureDetector
mGestureDetector = new GestureDetector(this, android_gesture_detector);
```

### 测试手势类

运行应用程序并开始与应用程序交互。手势的结果将显示在 Android Studio LogCat 窗口中。

![Test Output](img/db459bf68c909e044a3e38369b95eb69.png)

一旦检测到，您就可以在被覆盖的方法中构建应用程序功能。

## 捏手势

任何应用程序中另一个有用的手势是缩放 UI 元素的能力。Android 提供了`ScaleGestureDetector`类来处理缩放视图的挤压手势。下面的代码是一个简单的 Android 应用程序的实现，该应用程序使用`ScaleListener`类在`imageView`上执行挤压手势，并根据手指的移动来缩放手势。

```
import android.app.Activity;
import android.os.Bundle;
import android.view.MotionEvent;
import android.view.ScaleGestureDetector;
import android.widget.ImageView;
import android.widget.Toast;

public class MainActivity extends Activity {

    private ImageView imageView;
    private float scale = 1f;
    private ScaleGestureDetector detector;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        imageView=(ImageView)findViewById(R.id.imageView);
        detector = new ScaleGestureDetector(this,new ScaleListener());
    }

public boolean onTouchEvent(MotionEvent event) {
//  re-route the Touch Events to the ScaleListener class
        detector.onTouchEvent(event);
        return super.onTouchEvent(event);
    }

    private class ScaleListener extends ScaleGestureDetector.SimpleOnScaleGestureListener {

        float onScaleBegin = 0;
        float onScaleEnd = 0;

        @Override
        public boolean onScale(ScaleGestureDetector detector) {
            scale *= detector.getScaleFactor();
            imageView.setScaleX(scale);
            imageView.setScaleY(scale);
            return true;
        }

        @Override
        public boolean onScaleBegin(ScaleGestureDetector detector) {

            Toast.makeText(getApplicationContext(),"Scale Begin" ,Toast.LENGTH_SHORT).show();
            onScaleBegin = scale;

            return true;
        }

        @Override
        public void onScaleEnd(ScaleGestureDetector detector) {

            Toast.makeText(getApplicationContext(),"Scale Ended",Toast.LENGTH_SHORT).show();
            onScaleEnd = scale;

            if (onScaleEnd > onScaleBegin){
                Toast.makeText(getApplicationContext(),"Scaled Up by a factor of  " + String.valueOf( onScaleEnd / onScaleBegin ), Toast.LENGTH_SHORT  ).show();
            }

            if (onScaleEnd < onScaleBegin){
                Toast.makeText(getApplicationContext(),"Scaled Down by a factor of  " + String.valueOf( onScaleBegin / onScaleEnd ), Toast.LENGTH_SHORT  ).show();
            }

            super.onScaleEnd(detector);
        }
      }
}
```

`ScaleListener`类覆盖了三个方法`onScale`、`onScaleBegin`和`onScaleEnd`。在执行`onScale`方法的过程中，根据设备屏幕上手指移动的比例因子调整`imageView`的大小。

![Pinch Gesture](img/629619eca325eec8b7d78fe95ab01b9d.png)

## 2.运动事件

Android 简单手势检测器对基本手势很有用。但是对于任何寻找两个(或更多)触摸的手势，你需要看其他地方。为了实现自定义和更复杂的触摸机制，我们使用运动事件。在大多数应用程序中，你不需要弄脏你的手来实现你自己的触摸事件，但是如果你曾经想制作一个具有很酷的触摸事件的完全交互的应用程序，这个类就是解决方案。`MotionEvent`类的核心是通过覆盖`onTouchEvent()`回调来捕获活动上的触摸事件，或者通过使用`setOnTouchListener()`方法来侦听视图上的触摸事件来捕获视图上的触摸事件。视图触摸事件可以通过在 Activity 类上实现`View.OnTouchListener`来捕获，如下例所示。

每当一个或多个手指触摸屏幕时，Android 都会生成以下触摸事件。

`ACTION_DOWN`

对于触摸屏幕的第一个指针。开始新的接触。

`ACTION_MOVE`

触摸手势发生了变化。手指在动。

`ACTION_UP`

最后一个指针离开屏幕。

`ACTION_POINTER_DOWN`

对于进入屏幕的第一个指针之外的额外指针。(多点触控)

`ACTION_POINTER_UP`

当非主指针上升时发送。指针向上(多点触摸)

`ACTION_CANCEL`

触摸事件已被取消，其他东西控制了该事件。

为了展示`MotionEvent`类的用法，让我们考虑一个在实际应用中使用它的例子。手势存在的原因是为了与应用程序的 UI 进行交互，所以我将介绍一个简单的应用程序示例，它可以通过一个手指手势围绕主布局移动视图，通过两个手指手势拉伸视图，并通过三个手指手势旋转视图。如果你仔细想想，这些手势可能永远不会在真正的应用程序中一起使用，但由于这是出于演示的目的，我将在一个触摸事件中一起实现它们。

让我们从这些触摸事件开始。创建一个实现`View.OnTouchListener`的 Android 活动。在活动的布局文件中，设置主相对布局 id 为`rootLayout`。如示例中所实现的，这个活动的视图将能够响应用户在这个布局上的动作所触发的触摸事件。

这个想法是在根布局上添加`imageView`元素，每个元素都能够基于触摸事件进行操作。为了在布局上添加新的`imageView`,我们添加了一个按钮，每次点击它都会调用`Add_Image()`函数。

这个函数创建一个`imageView`，将它设置为一个图像资源，创建一组布局参数，将它们固定到`imageView`，将这个 imageView 添加到根布局，并将 Touch Listener 设置为这个视图。

```
 private void Add_Image() {
        final ImageView iv = new ImageView(this);
        iv.setImageResource(R.drawable.image);

        RelativeLayout.LayoutParams layoutParams = new RelativeLayout.LayoutParams(150, 150);

        iv.setLayoutParams(layoutParams);
        RootLayout.addView(iv, layoutParams);
        iv.setOnTouchListener(this);
    }
```

最重要的部分是视图`TouchListener`。收听者基于上述触摸事件执行动作。

在`ACTION_DOWN`事件中，我获得了触摸的 X 和 Y 位置，并移除了视图各自的边距，以获得视图中执行触摸的精确点。

在`ACTION_UP`事件期间，我实现了一个自定义的双击事件来删除执行该事件的视图。

在`ACTION_MOVE`期间，我实现了上面描述的酷功能的动作。关于`MotionEvent`类的一个很酷的事情是能够用`getPointerCount()`方法检测触摸屏幕的手指数量。

### 一指–移动视图。

获取视图的`LayoutParams`，并根据手指在屏幕上的移动进行重置。左边距等于触摸事件的位置减去在`ACTION_DOWN`期间设置的视图内触摸的 X 值，因此视图将根据手指在布局上的 X 值移动，并根据视图内触摸的位置相对对齐。

同样的逻辑也适用于上边距，但是基于 Y 轴。右边距和下边距设置为 500，这样当视图接触到布局的右边距和下边距时不会缩小，而是隐藏在它的后面。

```
RelativeLayout.LayoutParams Params = (RelativeLayout.LayoutParams) view.getLayoutParams();
                        Params.leftMargin = X - Position_X;
                        Params.topMargin = Y - Position_Y;
                        Params.rightMargin = -500;
                        Params.bottomMargin = -500;
                        view.setLayoutParams(Params);
```

### 两个手指–拉伸视图

获取视图的`LayoutParams`并调整视图的宽度和高度。在此调整大小操作期间，视图边距不会改变，而调整大小将基于第一个手指的移动而改变，因此大小将被设置为第一个手指在布局上的位置值。常量`Possition_X`和`Possition_Y`不影响大小调整，它们是为了视觉目的。

```
 RelativeLayout.LayoutParams layoutParams1 =  (RelativeLayout.LayoutParams) view.getLayoutParams();
                            layoutParams1.width = Position_X +(int)event.getX();
                            layoutParams1.height = Position_Y + (int)event.getY();
                            view.setLayoutParams(layoutParams1);
```

### 三个手指–旋转视图

视图的旋转非常简单。获取视图的当前旋转，给它添加一个浮点常量，并在三指`ACTION_MOVE`期间用`setRotation()`方法再次设置它。

```
view.setRotation(view.getRotation() + 10.0f);
```

请记住，在旋转事件之后，视图的大小调整将无法正常工作，因为视图的轴被旋转到其当前的旋转状态。

现在，MotionEvent 活动的完整代码如下:

```
import android.app.Activity;
import android.app.AlertDialog;
import android.content.DialogInterface;
import android.os.Bundle;
import android.view.MotionEvent;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.ImageView;
import android.widget.RelativeLayout;

public class MainActivity extends Activity implements View.OnTouchListener  {
    int clickCount;
    private ViewGroup RootLayout;
    private int Position_X;
    private int Position_Y;

    long startTime = 0 ;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        RootLayout = (ViewGroup) findViewById(R.id.rootLayout);

        //new image
        Button NewImage = (Button)findViewById(R.id.new_image_button);
        NewImage.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Add_Image();
            }
        });

        clickCount = 0;

    }

    private void Add_Image() {
        final ImageView iv = new ImageView(this);
        iv.setImageResource(R.drawable.image);

        RelativeLayout.LayoutParams layoutParams = new RelativeLayout.LayoutParams(150, 150);
        iv.setLayoutParams(layoutParams);
        RootLayout.addView(iv, layoutParams);
        iv.setOnTouchListener(this);
    }

    public boolean onTouch(final View view, MotionEvent event) {
        final int X = (int) event.getRawX();
        final int Y = (int) event.getRawY();

        int pointerCount = event.getPointerCount();

            switch (event.getAction() & MotionEvent.ACTION_MASK) {

                case MotionEvent.ACTION_DOWN:
                    RelativeLayout.LayoutParams layoutParams = (RelativeLayout.LayoutParams) view.getLayoutParams();
                    Position_X = X - layoutParams.leftMargin;
                    Position_Y = Y - layoutParams.topMargin;
                    break;

                case MotionEvent.ACTION_UP:
                    if (startTime == 0){

                        startTime = System.currentTimeMillis();

                    }else {
                        if (System.currentTimeMillis() - startTime < 200) {

                            AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
                            builder.setMessage("Are you sure you want to delete this?");
                            builder.setPositiveButton("Yes", new DialogInterface.OnClickListener() {
                                @Override
                                public void onClick(DialogInterface dialog, int which) {

                                    view.setVisibility(View.GONE);

                                }
                            });

                            builder.setNegativeButton("No", new DialogInterface.OnClickListener() {
                                @Override
                                public void onClick(DialogInterface dialog, int which) {
                                    dialog.dismiss();
                                }
                            });

                            AlertDialog alertDialog = builder.create();
                            alertDialog.show();

                        }

                        startTime = System.currentTimeMillis();

                    }
                    break;
                case MotionEvent.ACTION_POINTER_DOWN:
                    break;

    case MotionEvent.ACTION_POINTER_UP:
                    break;

                case MotionEvent.ACTION_MOVE:

                    if (pointerCount == 1){
                        RelativeLayout.LayoutParams Params = (RelativeLayout.LayoutParams) view.getLayoutParams();
                        Params.leftMargin = X - Position_X;
                        Params.topMargin = Y - Position_Y;
                        Params.rightMargin = -500;
                        Params.bottomMargin = -500;
                        view.setLayoutParams(Params);
                    }

                    if (pointerCount == 2){

                            RelativeLayout.LayoutParams layoutParams1 =  (RelativeLayout.LayoutParams) view.getLayoutParams();
                            layoutParams1.width = Position_X +(int)event.getX();
                            layoutParams1.height = Position_Y + (int)event.getY();
                            view.setLayoutParams(layoutParams1);
                    }

                    //Rotation
                    if (pointerCount == 3){
                        //Rotate the ImageView
                        view.setRotation(view.getRotation() + 10.0f);
                    }

                    break;
            }

// Schedules a repaint for the root Layout.
        RootLayout.invalidate();
        return true;
    }
}
```

### 测试 MotionEvent 应用

#### 移动视图

![Move Views](img/a551a931ffd5efe4a632e0fe93a77682.png)

#### 调整视图大小

![Resize Views](img/59a4f4a62ca88d6ec3ae070e47dfe26e.png)

#### 旋转视图

![Spin View](img/17f1df8d105bfa39121b5e9b1264f85c.png)

## 结论

在这篇文章中，我介绍了两种主要的 Android 手势类，并向您介绍了使用它们的基本知识。使用您在此学到的知识来改善您的应用程序的用户体验，如果您有任何问题或意见，请告诉我。

## 分享这篇文章