# 使用 Android 文本到语音转换创建智能助手

> 原文：<https://www.sitepoint.com/using-android-text-to-speech-to-create-a-smart-assistant/>

在本教程中，我将向您展示如何创建一个简单的 Android 应用程序，它可以监听用户的语音并将其转换为文本。然后，该应用程序将分析文本，并将其用作存储数据或回答用户的命令。

应用程序的用户界面很简单，只有一个位于全屏渐变背景中央的`ImageButton`。每次用户讲话时，他们按下按钮并讲话。

你可以在 [Github](https://github.com/sitepoint-editors/SpeechApplication) 上找到最终的项目。

![Create Project](img/4969ceeb40e558af963a47dd902d9c69.png)

## 创建应用程序

在 android Studio 中创建新项目，选择最低 API 级别 *18* 并添加一个*空活动*。这将是项目中唯一的活动。

要使视图全屏，打开 *AndroidManifest.xml* 并设置`android:theme="@style/Theme.AppCompat.NoActionBar"`。这将从我们的活动中隐藏`ActionBar`。

你现在有了一个带有`TextView`的全屏白色布局。为了改善它，给`RelativeLayout`添加一个渐变形状

右击*可绘制*文件夹，选择*新建- >可绘制资源文件*。称之为“后台”,并将代码替换为:

```
<?xml version="1.0" encoding="UTF-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle" >

    <gradient
        android:type="linear"
        android:startColor="#FF85FBFF"
        android:endColor="#FF008080"
        android:angle="45"/>
</shape> 
```

你可以随意改变颜色和角度。

布局内的`ImageButton`使用了来自[材料设计图标](https://design.google.com/icons/#ic_mic_none)的图像。下载并添加为`src`。

将 *activity_main.xml* 中的代码更新为:

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@drawable/background"
    android:id="@+id/rel"
    tools:context="com.example.theodhor.speechapplication.MainActivity">

    <ImageButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/microphoneButton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:src="@drawable/ic_mic_none_white_48dp"
        android:background="@null"/>

</RelativeLayout> 
```

## 讲

现在用户界面已经完成，下一步是`MainActivity`中的 Java 代码。

在`onCreate`方法之上声明一个`TextToSpeech`变量:

```
private TextToSpeech tts; 
```

在`onCreate`内添加:

```
tts = new TextToSpeech(this, new TextToSpeech.OnInitListener() {
    @Override
    public void onInit(int status) {
        if (status == TextToSpeech.SUCCESS) {
            int result = tts.setLanguage(Locale.US);
            if (result == TextToSpeech.LANG_MISSING_DATA || result == TextToSpeech.LANG_NOT_SUPPORTED) {
                Log.e("TTS", "This Language is not supported");
            }
            speak("Hello");

        } else {
            Log.e("TTS", "Initilization Failed!");
        }
    }
}); 
```

这将启动`TextToSpeech`服务。`speak()`方法接受一个`String`参数，这是您希望 Android 朗读的文本。

创建方法并添加以下代码:

```
private void speak(String text){
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
        tts.speak(text, TextToSpeech.QUEUE_FLUSH, null, null);    
    }else{
        tts.speak(text, TextToSpeech.QUEUE_FLUSH, null);
    }
} 
```

该方法中有一个`Build.VERSION`检查，因为对于 5.1 以上的 API 级别，`tts.speak(param,param,param)`已被否决

在`speak()`之后，创建另一个方法来在用户关闭应用程序时停止`TextToSpeech`服务:

```
@Override
public void onDestroy() {
    if (tts != null) {
        tts.stop();
        tts.shutdown();
    }
    super.onDestroy();
} 
```

在这个阶段，应用程序一旦启动就说“你好”。下一步是让它听。

## 倾听

要让应用程序监听，您将使用麦克风按钮。将此代码添加到`onCreate`:

```
findViewById(R.id.microphoneButton).setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        listen();
    }
}); 
```

点击`ImageButton`功能将调用该功能:

```
private void listen(){
    Intent i = new Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH);
    i.putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL, RecognizerIntent.LANGUAGE_MODEL_FREE_FORM);
    i.putExtra(RecognizerIntent.EXTRA_LANGUAGE, Locale.getDefault());
    i.putExtra(RecognizerIntent.EXTRA_PROMPT, "Say something");

    try {
        startActivityForResult(i, 100);
    } catch (ActivityNotFoundException a) {
        Toast.makeText(MainActivity.this, "Your device doesn't support Speech Recognition", Toast.LENGTH_SHORT).show();
    }
} 
```

该方法启动`listening`活动，该活动显示为带有文本提示的对话框。演讲的语言通过`Locale.getDefault()`方法从设备中获取。

`startActivityForResult(i, 100)`方法等待当前活动返回结果。`100`是附加到已启动活动的随机代码，可以是适合您的用例的任何数字。当一个结果从开始的活动返回时，它包含这个代码并使用它来区分多个结果。

要捕获已启动活动的结果，请添加这个重写的方法:

```
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
  super.onActivityResult(requestCode, resultCode, data);
  if(requestCode == 100){
      if (resultCode == RESULT_OK && null != data) {
          ArrayList<String> res = data.getStringArrayListExtra(RecognizerIntent.EXTRA_RESULTS);
          String inSpeech = res.get(0);
          recognition(inSpeech);
      }
  }
} 
```

该方法捕获来自活动的每个结果，并将`requestCode`用于语音识别器结果。如果`requestCode`等于`100`，则`resultCode`等于`OK`，由此结果得出的`data`不是`null`。你从`res.get(0)`中得到`result`字符串

创建一个名为`recognition`的新方法，它将接受一个`String`作为参数:

```
private void recognition(String text){
    Log.e("Speech",""+text);            
} 
```

在这个阶段，应用程序可以在用户单击麦克风按钮后进行监听，并将用户的语音转换为文本。结果打印在*错误日志*中。

## 学问

为了让应用程序更有趣，在这一步你要让应用程序能够学习简单的东西，比如你的名字。为此，您需要使用本地存储。

在`onCreate`方法上添加以下几行:

```
private SharedPreferences preferences;
private SharedPreferences.Editor editor;
private static final String PREFS = "prefs";
private static final String NAME = "name";
private static final String AGE = "age";
private static final String AS_NAME = "as_name"; 
```

然后，在`onCreate`内添加:

```
preferences = getSharedPreferences(PREFS,0);
editor = preferences.edit(); 
```

首先你需要让应用程序问一个问题，所以把`speak("Hello")`改成`speak("What is your name?")`

这里可以用一个简单的逻辑，所以当有人问“你叫什么名字？”，答案是“我叫多莉。”，从答案中取名字。一个简单的方法是用空格(" ")分割答案的字符串，得到最后一个索引的值。

更新`recognition`方法中的代码:

```
private void recognition(String text){
  Log.e("Speech",""+text);

  //creating an array which contains the words of the answer
  String[] speech = text.split(" ");

  //the last word is our name
  String name = speech[speech.length-1];

  //we got the name, we can put it in local storage and save changes
  editor.putString(NAME,name).apply();  

  //make the app tell our name
  speak("Your name is "+preferences.getString(NAME,null));
} 
```

`recognition`方法使用来自用户语音的所有结果。由于语音可能不同，您可以使用它们可能包含的某些单词来区分它们。

例如，此方法中的代码可能是:

```
private void recognition(String text){
    Log.e("Speech",""+text);
    String[] speech = text.split(" ");

    //if the speech contains these words, the user is saying their name
    if(text.contains("my name is")){
        String name = speech[speech.length-1];
        Log.e("Your name", "" + name);
        editor.putString(NAME,name).apply();
        speak("Your name is "+preferences.getString(NAME,null));
    }
} 
```

但这仍然是与 app 的简单交互。你可以让它知道你的年龄，甚至给它一个名字。

在同一方法中，尝试以下简单条件:

```
//This must be the age

//Just speak: I am x years old.
if(text.contains("years") && text.contains("old")){
    String age = speech[speech.length-3];
    Log.e("THIS", "" + age);
    editor.putString(AGE, age).apply();
}

//Then ask it for your age
if(text.contains("how old am I")){
    speak("You are "+preferences.getString(AGE,null)+" years old.");
} 
```

这款应用可以告诉你时间:

```
//Ask: What time is it?
if(text.contains("what time is it")){
    SimpleDateFormat sdfDate = new SimpleDateFormat("HH:mm");//dd/MM/yyyy
    Date now = new Date();
    String[] strDate = sdfDate.format(now).split(":");
    if(strDate[1].contains("00"))strDate[1] = "o'clock";
    speak("The time is " + sdfDate.format(now));
} 
```

## 说得好

在 GitHub 项目中，我提供了更多的例子，供你试验和构建你自己的 Android 助手。

我希望你喜欢这个教程，并与你的手机进行了有益的交谈。任何问题或意见，请让我知道下面。

## 分享这篇文章