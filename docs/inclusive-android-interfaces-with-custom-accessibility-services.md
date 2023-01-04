# 具有定制辅助功能服务的包容性 Android 界面

> 原文：<https://www.sitepoint.com/inclusive-android-interfaces-with-custom-accessibility-services/>

*这篇文章由[阿德里安·桑杜](http://www.adriansandu.com/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

花时间开发一个所有人都可以访问的应用程序是授权潜在用户的一项重要任务。

Android 内置了[辅助功能](http://developer.android.com/guide/topics/ui/accessibility/index.html)来帮助以不同方式与设备互动的用户。这些包括自动完成，屏幕阅读器(对讲)和文本到语音输出。那么，为什么要创建自己的定制可访问性服务呢？

创建您自己的辅助功能服务允许您为特定用户群定制适合您的应用程序的应用程序。

## 处理辅助功能服务

Android 框架为屏幕上的所有内容构建了一个事件流，辅助功能服务订阅这个流，关注不同的元素。

在本教程中，我将介绍如何创建一个可访问性服务，该服务过滤用户的操作，并基于这些操作使用 TextToSpeech 给出反馈。

让我们开始构建吧，你可以在 [GitHub](https://github.com/sitepoint-editors/AccessibilityService) 上找到该项目的代码。打开 Android Studio，新建一个*空白活动*项目。

创建一个文件名为*CustomAccessibilityService.java*的类，该类扩展`AccessibilityService`并实现覆盖方法(`onAccessibilityEvent`和`onInterrupt`):

```
public class CustomAccessibilityService extends AccessibilityService {

    @Override
    public void onAccessibilityEvent(AccessibilityEvent event) {

    }

    @Override
    public void onInterrupt() {

    }
}
```

### 在清单文件中声明 AccessibilityService

辅助功能服务必须在应用程序的清单文件中显式声明:

```
<application>
  ...
  <!--Declare AccessibilityService-->
  <service
      android:name=".CustomAccessibilityService"
      android:permission="android.permission.BIND_ACCESSIBILITY_SERVICE">
      <intent-filter>
          <action android:name="android.accessibilityservice.AccessibilityService" />
      </intent-filter>
      <meta-data
          android:name="android.accessibilityservice"
          android:resource="@xml/service_configuration" />
  </service>
  ...
</application>
```

该声明指定了具有权限`BIND_ACCESSIBILITY_SERVICE`的`AccessibilityService`类的名称，该权限将该服务区分为可访问性服务。元数据包含服务配置资源文件。

### 配置辅助功能服务

配置辅助功能服务包括指定服务处理的辅助功能事件的类型。

在 *res/xml/* 中创建*service _ configuration . XML*，并添加以下内容:

```
<accessibility-service xmlns:android="http://schemas.android.com/apk/res/android"
    android:accessibilityEventTypes="typeViewClicked|typeViewScrolled"
    android:accessibilityFeedbackType="feedbackAllMask"
    android:canRetrieveWindowContent="true"
    android:notificationTimeout="100"
    android:packageNames="valdioveliu.accessibilityservice" />
```

这里最重要的设置是代表该服务订阅的事件流的`accessibilityEventTypes`。

一个重要的设置是`packageNames`声明。如果不为辅助功能服务指定包名，它将在系统范围内运行。您可以为一个可访问性服务声明多个`packageName`。

### 实现可访问性服务类

可访问性服务类是所有奇迹发生的地方。将以下内容添加到*CustomAccessibilityService.java*:

```
public class CustomAccessibilityService extends AccessibilityService {

    private TextToSpeech textToSpeech;

    //Configure the Accessibility Service
    @Override
    protected void onServiceConnected() {
        Toast.makeText(getApplication(), "onServiceConnected", Toast.LENGTH_SHORT).show();

        //Init TextToSpeech
        textToSpeech = new TextToSpeech(getApplication(), new TextToSpeech.OnInitListener() {
            @Override
            public void onInit(int status) {
                if (status == TextToSpeech.SUCCESS) {
                    int result = textToSpeech.setLanguage(Locale.US);
                    if (result == TextToSpeech.LANG_MISSING_DATA || result == TextToSpeech.LANG_NOT_SUPPORTED) {
                        Log.e("TextToSpeech", "Language not supported");
                    }
                } else {
                    Log.e("TextToSpeech", "Initialization Failed! :( ");
                }
            }
        });
    }

    //Respond to AccessibilityEvents
    @Override
    public void onAccessibilityEvent(AccessibilityEvent event) {

        if (event.getEventType() == AccessibilityEvent.TYPE_VIEW_CLICKED) {
            Toast.makeText(getApplication(), event.getText().toString(), Toast.LENGTH_SHORT).show();

            //TextToSpeech
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
                textToSpeech.speak(event.getText().toString(), TextToSpeech.QUEUE_FLUSH, null, "TextToSpeech_ID");
            } else if (Build.VERSION.SDK_INT < Build.VERSION_CODES.LOLLIPOP) {
                textToSpeech.speak(event.getText().toString(), TextToSpeech.QUEUE_FLUSH, null);
            }

        } else if (event.getEventType() == AccessibilityEvent.TYPE_VIEW_SCROLLED) {

            //RecyclerView Scrolled
            //TextToSpeech
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
                textToSpeech.speak("Scrolling", TextToSpeech.QUEUE_FLUSH, null, "TextToSpeech_ID");
            } else if (Build.VERSION.SDK_INT < Build.VERSION_CODES.LOLLIPOP) {
                textToSpeech.speak("Scrolling", TextToSpeech.QUEUE_FLUSH, null);
            }
        }

    }

    @Override
    public void onInterrupt() {
        //Interrupt the Accessibility service
        //Stop TextToSpeech
        if (textToSpeech != null) {
            textToSpeech.stop();
            textToSpeech.shutdown();
        }
    }

}
```

`onAccessibilityEvent`方法响应传入的事件，当用户触发了一个与订阅的可访问性事件相匹配的可访问性事件时，它就跳进去。

当 Android 框架想要停止服务正在做的事情时，调用`onInterrupt`方法。

系统成功绑定服务后,`onServiceConnected`设置`TextToSpeech`。

此辅助功能服务仅适用于可点击的视图。这意味着如果用户试图点击一个不可点击的视图，例如文本视图，它将不起作用。要在这种情况下使用服务，您需要向视图添加以下属性。

```
<TextView
...
   android:clickable="true"
.../>
```

**注意**:要在设备上使用辅助功能服务，您的用户必须在其设备设置中启用辅助功能服务。

## 结论

在本文中，我介绍了实现可访问性服务所需的所有内容。为了让你的应用程序真正具有可访问性，我建议你遵循[Android 指南](http://developer.android.com/guide/topics/ui/accessibility/apps.html)、[可访问性事件的详细描述](http://developer.android.com/reference/android/view/accessibility/AccessibilityEvent.html)及其方法以及 [SitePoint 自己的 Android 可访问性特性指南](https://www.sitepoint.com/introduction-android-accessibility-features/)。

## 分享这篇文章