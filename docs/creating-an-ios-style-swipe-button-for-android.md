# 如何为 Android 创建一个 iOS 风格的滑动按钮

> 原文：<https://www.sitepoint.com/creating-an-ios-style-swipe-button-for-android/>

即使你以前从未接触过 iPhone，你也可能熟悉臭名昭著的“滑动解锁”按钮，并且可能一直想要一个用于 Android 应用程序的滑动按钮。

![the good old iphone 'slide to unlock'](img/5b63402a562cb19a83b59e834eea2f7f.png)

这个按钮的最初目的是防止设备在口袋中意外解锁。在您创建的移动应用程序中，可能有一个类似的用例，您想让用户选择确认一个明确的操作。优步的司机应用程序有这样一个按钮，司机从左向右滑动以确认他们想开始一段旅程。

通常解决方案是用弹出窗口提示用户确认操作，但这不是一个有创意的解决方案。让我们尝试为 Android 创建自己的滑动按钮。

![Final Swipe Button](img/ceb455a6189f432780d80d3159408795.png)

该项目的最终代码在 [GitHub](https://github.com/sitepoint-editors/SwipeButtonExample) 上。

## 入门指南

在 Android Studio 中用一个*空白活动*创建一个新项目。

添加一个名为`SwipeButton`的类，它扩展了[按钮](http://developer.android.com/reference/android/widget/Button.html)类，并向其中添加以下内容:

```
import android.content.Context;
import android.util.AttributeSet;
import android.widget.Button;

public class SwipeButton extends Button {
    public SwipeButton(Context context) {
        super(context);
    }

    public SwipeButton(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public SwipeButton(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

} 
```

对于所需的功能，您必须使用按钮的`onTouchEvent`监听器。在侦听器中，当用户按下、划过或释放按钮时，您会捕获它。通过向`SwipeButton`类添加以下内容来覆盖`onTouchEvent`:

```
@Override
public boolean onTouchEvent(MotionEvent event) {

  switch (event.getAction()) {
      // when user first touches the screen we get x and y coordinate
      case MotionEvent.ACTION_DOWN: {
          //when user first touches the screen change the button text to desired value
          break;
      }
      case MotionEvent.ACTION_UP: {
          //when the user releases touch then revert back the text
          break;
      }
      case MotionEvent.ACTION_MOVE: {
          //here we'll capture when the user swipes from left to right and write the logic to create the swiping effect
          break;
      }
  }

  return super.onTouchEvent(event);
} 
```

上面的代码处理三种情况，我将详细介绍这三种情况:

1.  当用户第一次触摸按钮时
2.  当用户滑动按钮时。
3.  当用户在完成滑动或中间某处释放按钮时。

### 1.当用户第一次触摸按钮时

您需要执行以下操作:

1.  从`onTouchEvent`回调中的`MotionEvent`对象获取用户触摸按钮位置的 x 和 y 坐标。
2.  更改按钮的文本或颜色。

代码如下:

```
public class SwipeButton extends Button {
  private float x1;
  //x coordinate of where user first touches the button
  private float y1;
  //y coordinate of where user first touches the button
  private String originalButtonText;
  //the text on the button
  private boolean confirmThresholdCrossed;
  //whether the threshold distance beyond which action is considered confirmed is crossed or not
  private boolean swipeTextShown;
  //whether the text currently on the button is the text shown while swiping or the original text

  ...

  @Override
  public boolean onTouchEvent(MotionEvent event) {

      switch (event.getAction()) {   
          case MotionEvent.ACTION_DOWN: {               
              x1 = event.getX();
              y1 = event.getY();
              // when user first touches the screen we get x and y coordinate

              this.originalButtonText = this.getText().toString();
              //save the original text on the button

              confirmThresholdCrossed = false;
              //action hasn't been confirmed yet

              if (!swipeTextShown) {
                  this.setText(">> SWIPE TO CONFIRM >>");
                  //change the text on the button to indicate that the user is supposed to swipe the button to confirm
                  swipeTextShown = true;
              }
              break;
          }
          ...
      }

      return super.onTouchEvent(event);
  }
  } 
```

### 2.当用户滑动按钮时

当用户滑动按钮时，需要发生以下情况:

1.  如果需要，更改文本。
2.  当用户滑动按钮时，通过操纵渐变来显示滑动效果。

[LinearGradient](http://developer.android.com/reference/android/graphics/LinearGradient.html) 用于显示三种颜色渐变效果的滑动效果。当用户滑动时，这种渐变从左向右移动，产生所需的效果。

```
public class SwipeButton extends Button {

  ...
  private boolean swiping = false;
  private float x2Start;
  //whether the text currently on the button is the text shown while swiping or the original text

  @Override
  public boolean onTouchEvent(MotionEvent event) {
    switch (event.getAction()) {
      ...
      case MotionEvent.ACTION_MOVE: {
          //here we'll capture when the user swipes from left to right and write the logic to create the swiping effect

          float x2 = event.getX();
          float y2 = event.getY();

          if(!swiping){
              x2Start = event.getX();
              //this is to capture at what x swiping started
              swiping = true;
          }

          //if left to right sweep event on screen
          if (x1 < x2 && !confirmThresholdCrossed) {
              this.setBackgroundDrawable(null);

              ShapeDrawable mDrawable = new ShapeDrawable(new RectShape());

              int gradientColor1 = 0xFF333333;
              int gradientColor2 = 0xFF666666;
              int gradientColor2Width = 50;
              int gradientColor3 = 0xFF888888;
              double actionConfirmDistanceFraction = 0.6;
              //We'll get to how to be able to customize these values for each instance of the button

              Shader shader = new LinearGradient(x2, 0, x2 - gradientColor2Width, 0,
                      new int[]{gradientColor3, gradientColor2, gradientColor1},
                      new float[]{0, 0.5f, 1},
                      Shader.TileMode.CLAMP);

              mDrawable.getPaint().setShader(shader);
              this.setBackgroundDrawable(mDrawable);

              if (swipeTextShown == false) {
                  this.setText(">> SWIPE TO CONFIRM >> ");
                  //change text while swiping
                  swipeTextShown = true;
              }

              if ((x2-x2Start) > (this.getWidth() * actionConfirmDistanceFraction)) {
                  Log.d("CONFIRMATION", "Action Confirmed! Read on to find how to get your desired callback here");
                  //confirm action when swiped upto the desired distance
                  confirmThresholdCrossed = true;
              }
          }
      }
      ... 
```

### 3.当用户释放按钮时

现在，当用户释放按钮时，将有两种可能性:

1.  用户在确认动作前释放。意味着在越过阈值距离之前，用户需要滑动来确认动作。
2.  用户在确认动作后释放，意味着在越过阈值距离后。

这两者都包含在下面的代码片段中:

```
...
case MotionEvent.ACTION_UP: {
  //when the user releases touch then revert back the text
  swiping = false;
  float x2 = event.getX();
  int buttonColor = swipeButtonCustomItems.getPostConfirmationColor();
  String actionConfirmText = swipeButtonCustomItems.getActionConfirmText() == null ? this.originalButtonText : swipeButtonCustomItems.getActionConfirmText();
  //if you choose to not set the confirmation text, it will set to the original button text;

  this.setBackgroundDrawable(null);
  this.setBackgroundColor(buttonColor);
  swipeTextShown =  false;

  if ((x2-x2Start) <= (this.getWidth() * swipeButtonCustomItems.getActionConfirmDistanceFraction())) {
      Log.d("CONFIRMATION", "Action not confirmed");
      this.setText(originalButtonText);
      swipeButtonCustomItems.onSwipeCancel();
      confirmThresholdCrossed = false;

  } else {
      Log.d("CONFIRMATION", "Action confirmed");
      this.setText(actionConfirmText);
  }

  break;
}
... 
```

一些属性，比如渐变颜色，现在已经被硬编码了。这些将为按钮的每个实例设置，并且能够在用户使用按钮执行某些操作时设置回调函数。

### 设置属性

您可以为每个`SwipeButton`实例定制的属性有:

*   三种渐变颜色。
*   第二种渐变颜色覆盖的宽度。
*   用户需要滑动以确认动作的`Button`宽度的一部分。
*   当用户按下或滑动按钮时出现的文本。

您还可以提供选项，以便在用户出现以下情况时分配回调:

*   按下按钮。
*   将按钮向上滑动到所需的距离进行确认。
*   释放按钮而不确认。

要接受这些属性和回调，创建另一个类，并使其成为抽象的。

```
public abstract class SwipeButtonCustomItems {
    //These are the default values if we don't choose to set them later:
    public int gradientColor1 = 0xFF333333;
    public int gradientColor2 = 0xFF666666;
    public int gradientColor2Width = 50;
    public int gradientColor3 = 0xFF888888;
    public int postConfirmationColor = 0xFF888888;
    public double actionConfirmDistanceFraction = 0.7;
    public String buttonPressText = ">>   SWIPE TO CONFIRM   >> ";

    public String actionConfirmText = null;

    public int getGradientColor1() {
        return gradientColor1;
    }

    public SwipeButtonCustomItems setGradientColor1(int gradientColor1) {
        this.gradientColor1 = gradientColor1;
        return this;
    }

    public int getGradientColor2() {
        return gradientColor2;
    }

    public SwipeButtonCustomItems setGradientColor2(int gradientColor2) {
        this.gradientColor2 = gradientColor2;
        return this;
    }

    public int getGradientColor2Width() {
        return gradientColor2Width;
    }

    public SwipeButtonCustomItems setGradientColor2Width(int gradientColor2Width) {
        this.gradientColor2Width = gradientColor2Width;
        return this;
    }

    public int getGradientColor3() {
        return gradientColor3;
    }

    public SwipeButtonCustomItems setGradientColor3(int gradientColor3) {
        this.gradientColor3 = gradientColor3;
        return this;
    }

    public double getActionConfirmDistanceFraction() {
        return actionConfirmDistanceFraction;
    }

    public SwipeButtonCustomItems setActionConfirmDistanceFraction(double actionConfirmDistanceFraction) {
        this.actionConfirmDistanceFraction = actionConfirmDistanceFraction;
        return this;
    }

    public String getButtonPressText() {
        return buttonPressText;
    }

    public SwipeButtonCustomItems setButtonPressText(String buttonPressText) {
        this.buttonPressText = buttonPressText;
        return this;
    }

    public String getActionConfirmText() {
        return actionConfirmText;
    }

    public SwipeButtonCustomItems setActionConfirmText(String actionConfirmText) {
        this.actionConfirmText = actionConfirmText;
        return this;
    }

    public int getPostConfirmationColor() {
        return postConfirmationColor;
    }

    public SwipeButtonCustomItems setPostConfirmationColor(int postConfirmationColor) {
        this.postConfirmationColor = postConfirmationColor;
        return this;
    }

    //These methods listed below can be overridden in the instance of SwipeButton
    public void onButtonPress(){

    }

    public void onSwipeCancel(){

    }

    abstract public void onSwipeConfirm();
} 
```

现在，在主`SwipeButton`类中需要一个 setter，它引入了这个抽象类的一个实例。使用 setter，您可以设置所需的属性和回调。涵盖了所有案例，从抽象类实例中提取了属性和回调，下面是`SwipeButton`类现在的样子:

```
public class SwipeButton extends Button {

    private float x1;
    //x coordinate of where user first touches the button
    private float y1;
    //y coordinate of where user first touches the button
    private String originalButtonText;
    //the text on the button
    private boolean confirmThresholdCrossed;
    //whether the threshold distance beyond which action is considered confirmed is crossed or not
    private boolean swipeTextShown;
    private boolean swiping = false;
    private float x2Start;
    //whether the text currently on the button is the text shown while swiping or the original text

    private SwipeButtonCustomItems swipeButtonCustomItems;
    //in this instance of the class SwipeButtonCustomItems we can accept callbacks and other params like colors

    public SwipeButton(Context context) {
        super(context);
    }

    public SwipeButton(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public SwipeButton(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    public void setSwipeButtonCustomItems(SwipeButtonCustomItems swipeButtonCustomItems) {
        //setter for swipeButtonCustomItems
        this.swipeButtonCustomItems = swipeButtonCustomItems;
    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {

        switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN: {
                // when user first touches the screen we get x and y coordinate
                x1 = event.getX();
                y1 = event.getY();

                this.originalButtonText = this.getText().toString();

                confirmThresholdCrossed = false;

                if (!swipeTextShown) {
                    this.setText(swipeButtonCustomItems.getButtonPressText());
                    swipeTextShown = true;
                }

                swipeButtonCustomItems.onButtonPress();
                break;
            }
            case MotionEvent.ACTION_MOVE: {
                //here we'll capture when the user swipes from left to right and write the logic to create the swiping effect

                float x2 = event.getX();
                float y2 = event.getY();

                if(!swiping){
                    x2Start = event.getX();
                    //this is to capture at what x swiping started
                    swiping = true;
                }

                //if left to right sweep event on screen
                if (x1 < x2 && !confirmThresholdCrossed) {
                    this.setBackgroundDrawable(null);

                    ShapeDrawable mDrawable = new ShapeDrawable(new RectShape());

                    int gradientColor1 = swipeButtonCustomItems.getGradientColor1();
                    int gradientColor2 = swipeButtonCustomItems.getGradientColor2();
                    int gradientColor2Width = swipeButtonCustomItems.getGradientColor2Width();
                    int gradientColor3 = swipeButtonCustomItems.getGradientColor3();
                    double actionConfirmDistanceFraction = swipeButtonCustomItems.getActionConfirmDistanceFraction();
                    //Note that above we replaced the hard coded values by those from the SwipeButtonCustomItems instance.

                    Shader shader = new LinearGradient(x2, 0, x2 - gradientColor2Width, 0,
                            new int[]{gradientColor3, gradientColor2, gradientColor1},
                            new float[]{0, 0.5f, 1},
                            Shader.TileMode.CLAMP);

                    mDrawable.getPaint().setShader(shader);
                    this.setBackgroundDrawable(mDrawable);

                    if (swipeTextShown == false) {
                        this.setText(swipeButtonCustomItems.getButtonPressText());
                        //change text while swiping
                        swipeTextShown = true;
                    }

                    if ((x2-x2Start) > (this.getWidth() * actionConfirmDistanceFraction)) {
                        Log.d("CONFIRMATION", "Action Confirmed!");
                        //Note that below we inserted the desired callback from the SwipeButtonCustomItem instance.
                        swipeButtonCustomItems.onSwipeConfirm();
                        //confirm action when swiped upto the desired distance
                        confirmThresholdCrossed = true;
                    }

                }

                break;
            }
            case MotionEvent.ACTION_UP: {
                //when the user releases touch then revert back the text
                swiping = false;
                float x2 = event.getX();
                int buttonColor = swipeButtonCustomItems.getPostConfirmationColor();
                String actionConfirmText = swipeButtonCustomItems.getActionConfirmText() == null ? this.originalButtonText : swipeButtonCustomItems.getActionConfirmText();
                //if you choose to not set the confirmation text, it will set to the original button text;

                this.setBackgroundDrawable(null);
                this.setBackgroundColor(buttonColor);
                swipeTextShown =  false;

                if ((x2-x2Start) <= (this.getWidth() * swipeButtonCustomItems.getActionConfirmDistanceFraction())) {
                    Log.d("CONFIRMATION", "Action not confirmed");
                    this.setText(originalButtonText);
                    swipeButtonCustomItems.onSwipeCancel();
                    confirmThresholdCrossed = false;

                } else {
                    Log.d("CONFIRMATION", "Action confirmed");
                    this.setText(actionConfirmText);
                }

                break;
            }
        }

        return super.onTouchEvent(event);
    }
} 
```

要在活动或片段中使用`SwipeButton`,请将相应的 xml 元素添加到布局 xml 中:

```
<com.package.path.SwipeButton
    android:id="@+id/my_swipe_button"
    android:layout_width="400dp"
    android:layout_height="50dp"
    android:text="Button"
    android:layout_below="@id/hello_world"
    android:background="#888888"
    android:textColor="#ffffff"
    /> 
```

要分配回调和定制属性，如颜色，将以下内容添加到您的`MainActivity`:

```
public class MainActivity extends AppCompatActivity {

  @Override
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity_main);
      SwipeButton mSwipeButton = (SwipeButton) findViewById(R.id.my_swipe_button);

      SwipeButtonCustomItems swipeButtonSettings = new SwipeButtonCustomItems() {
          @Override
          public void onSwipeConfirm() {
              Log.d("NEW_STUFF", "New swipe confirm callback");
          }
      };

      swipeButtonSettings
              .setButtonPressText(">> NEW TEXT! >>")
              .setGradientColor1(0xFF888888)
              .setGradientColor2(0xFF666666)
              .setGradientColor2Width(60)
              .setGradientColor3(0xFF333333)
              .setPostConfirmationColor(0xFF888888)
              .setActionConfirmDistanceFraction(0.7)
              .setActionConfirmText("Action Confirmed");

      if (mSwipeButton != null) {
          mSwipeButton.setSwipeButtonCustomItems(swipeButtonSettings);
      }
  }
} 
```

## 用例

使用这种类型的按钮交互可能是烦人的确认警告的一种有趣的替代方式。我很想知道你会有什么用例，请在下面的评论中添加你的想法。

## 分享这篇文章