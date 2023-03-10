# 使用 React 本机模块访问平台 API

> 原文：<https://www.sitepoint.com/access-platform-apis-with-react-native-modules/>

*这篇文章由[沃恩·安切塔](https://www.sitepoint.com/author/wancheta/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

来自脸书的 [React Native](https://facebook.github.io/react-native/) 是流行的 [React](https://facebook.github.io/react/) JavaScript 库的补充，用于创建本地移动应用。事实证明，它比其他框架更受欢迎，性能更好，功能更丰富，但有时它的功能集会有所欠缺。在这些时候，React Native 有一种很好的方式来创建自己的模块，用于访问尚不支持的本机 API。在本教程中，我将向您展示如何创建 react Native 模块，该模块公开 Android `MediaPlayer` api 以实现 React Native。

你可以在 [GitHub](https://github.com/sitepoint-editors/react-native-android-audio-player-native-module) 上找到该项目的完整代码。

## React Native 入门

SitePoint 有安装 React Native 的完整指南[在这里](https://www.sitepoint.com/quick-tip-installing-react-native/)。安装完成后，使用以下命令创建一个新项目:

```
react-native init ReactNativeModuleTutorial 
```

这将在您当前的工作目录中创建一个名为*ReactNativeModuleTutorial*的新文件夹。使用以下命令导航到该文件夹并在您的设备或模拟器上运行新项目。

**注意**:如果你使用的是 Android 模拟器，那么你必须在运行命令之前启动模拟器。

```
react-native run-android 
```

这将在项目中创建所有必需的文件和文件夹，您应该会在模拟器上看到下面的屏幕。

![Application Screenshot](img/e3f696f186bfa94059a735762a8933a6.png)

## 创建 React 原生 Android 原生模块

既然您已经设置了项目，那么是时候创建一个 React 本机模块了。在以下目录中创建一个名为 *myaudiomodule* 的新文件夹:

*ReactNativeModuleTutorial/Android/app/src/main/Java/com/ReactNativeModuleTutorial*

![Directory Structure](img/d9a01e00651161339387c19a76639cd8.png)

要创建一个简单的本机模块，至少需要两个文件。

1.  反应包类
2.  一个扩展了`ReactContextBaseJavaModule`的 java 类

React 包文件负责将不同的模块打包在一个文件中，以便稍后在 JavaScript 代码中使用。在 *myaudioplayer* 文件夹中创建一个名为*MyAudioPlayerPackage.java*的文件。您不必太关注这个文件，因为重要的部分是将模块(您将很快创建)添加到`createNativeModules`方法内的包中。如果您决定创建多个模块文件，那么您也需要在这里添加这些文件。

将以下内容添加到*MyAudioPlayerPackage.java*:

```
package com.reactnativemoduletutorial.myaudioplayer;

import com.facebook.react.ReactPackage;
import com.facebook.react.bridge.JavaScriptModule;
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.uimanager.ViewManager;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class MyAudioPlayerPackage implements ReactPackage {
    @Override
    public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
        List<NativeModule> modules = new ArrayList<>();
        modules.add(new MyAudioPlayerModule(reactContext)); // adding the module to package
        return modules;
    }
    @Override
    public List<Class<? extends JavaScriptModule>> createJSModules() {
        return Collections.emptyList();
    }
    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
        return Collections.emptyList();
    }
} 
```

是时候进入编写模块的激动人心的部分了，您将在稍后的 JavaScript 代码中使用这个模块。在同一个文件夹中创建另一个名为*MyAudioPlayerModule.java*的文件。在这个文件中，您将实现模块的所有逻辑，并访问 React Native 中尚未提供的 Android API。

```
package com.reactnativemoduletutorial.myaudioplayer;

import com.facebook.react.bridge.ReactContextBaseJavaModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.bridge.ReactMethod;
import com.facebook.react.bridge.Callback;
import java.util.Map;

// these classes are required for playing the audio
import android.media.MediaPlayer;
import android.media.AudioManager;

public class MyAudioPlayerModule extends ReactContextBaseJavaModule {

  private static MediaPlayer mediaPlayer = null;

  public MyAudioPlayerModule(ReactApplicationContext reactContext) {
    super(reactContext);
  }

   @Override
   public String getName() {
     return "MyAudioPlayer";
   }

} 
```

这个包是`com.reactnativemoduletutorial.myaudioplayer`的，因为你在*我的音频播放器*文件夹中。如果你相应地修改了这个包，你也可以在*reactnativemoduletorial*文件夹中的任何地方创建这些文件。

代码首先导入创建模块所需功能所需的所有类。每个模块类都扩展了`ReactContextBaseJavaModule`并且`ReactContextBaseJavaModule`需要`getName`方法。方法允许您为 JavaScript 代码中用来访问模块的模块设置一个名称。

```
@Override
public String getName() {
  return "MyAudioPlayer";
} 
```

用`@ReactMethod`标注的方法可以在 JavaScript 代码中访问，这些桥方法总是返回类型`void`。您必须以这种方式声明您想在 JavaScript 代码中使用的每个方法。

我在模块中创建了一些播放音频文件的方法，但是音频播放器的实现取决于你，你可以随意为播放器编写自己的代码。

```
@ReactMethod
public void preparePlayer(String url) {
  try{
    if (mediaPlayer != null) {
      mediaPlayer.release();
      mediaPlayer = null;
    }
    mediaPlayer = new MediaPlayer();
    mediaPlayer.setAudioStreamType(AudioManager.STREAM_MUSIC);
    mediaPlayer.setDataSource(url);
    mediaPlayer.setLooping(true);
    mediaPlayer.prepareAsync();
  }catch(Exception e){  }
}

@ReactMethod
public void play() {
  try{
    if (mediaPlayer != null) {
      if (!mediaPlayer.isPlaying()) {
        mediaPlayer.start();
      }
    }
  }catch(Exception e){}
}

@ReactMethod
public void pause(){
  try{
    if (mediaPlayer != null) {
      if (mediaPlayer.isPlaying()) {
        mediaPlayer.pause();
      }
    }
  }catch(Exception e){}
} 
```

代码是合理的自我解释，使用 Android 中可用的`MediaPlayer`类设置音频播放器、播放和暂停的方法。

编写本机模块的重要部分是创建接受在特定任务后调用的回调方法的方法。这就是将值从 Java 传递到 JavaScript 的方法。

创建一个名为`setOnPreparedCallback`的新方法，它将一个回调方法作为参数，并在音频文件准备好播放时触发这个回调。

```
@ReactMethod
public void setOnPreparedCallback(Callback onPrepared){
  final Callback onPreparedCallback = onPrepared;
  try{
    mediaPlayer.setOnPreparedListener(new MediaPlayer.OnPreparedListener() {
      @Override
      public void onPrepared(MediaPlayer player) {
        try{
          onPreparedCallback.invoke(mediaPlayer.getDuration()); // invoking the callback with duration as argument
        }catch(Exception e){}
      }
    });
  }catch(Exception e){}
} 
```

最后一步是告诉 React Native 关于这个包的信息。在*reactnativemoduletorial/Android/app/src/main/Java/com/reactnativemoduletorial/*中编辑*MainApplication.java*，从 *myaudioplayer* 文件夹中导入*MyAudioPlayerPackage.java*。

```
import com.reactnativemoduletutorial.myaudioplayer.MyAudioPlayerPackage; 
```

将 *getPackages* 方法更新如下:

```
return Arrays.<ReactPackage>asList(
    new MainReactPackage()
    new MyAudioPlayerPackage() // the line added
); 
```

现在，您可以在 React 本机应用程序中使用该模块。在项目的根目录中打开 *index.android.js* ,粘贴以下代码，替换已经存在的代码。我设计了一个简单的应用程序，有播放和暂停按钮，3 个应用程序当前状态的指示器。

```
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  ToastAndroid,
  View,
  NativeModules,
  TouchableHighlight
} from 'react-native';

var MyAudioPlayer  = NativeModules.MyAudioPlayer;

var buttonStyles = { marginTop: 8, backgroundColor: '#dddddd', padding: 10 };
var statStyle = { flex: 0.5, backgroundColor: '#cccccc', padding: 8, borderColor: '#ffffff', borderWidth: 1, margin: 2 };

class ReactNativeModuleTutorial extends Component {
  constructor(props){
    super(props);
    this.state = {
      mp3Url: 'http://www.example.com/audio.mp3',
      prepared: false,
      playing: false,
      duration: 0
    };
  }
  componentDidMount(){
    MyAudioPlayer.preparePlayer(this.state.mp3Url);
    MyAudioPlayer.setOnPreparedCallback((duration) => {
      this.setState({ prepared: true, duration: duration });
      ToastAndroid.show('Audio prepared', ToastAndroid.LONG);
    });
  }
  playSound(){
    if (this.state.prepared === true) {
      this.setState({ playing: true });
      MyAudioPlayer.play();
      return true;
    }
    return false;
  }
  pauseSound(){
    if (this.state.prepared === true && this.state.playing === true) {
      MyAudioPlayer.pause();
      this.setState({ playing: false })
      return true;
    }
    return false;
  }
  render() {
    return (
      <View style={{ flex:1, alignItems: 'stretch', backgroundColor: '#F5FCFF' }}>
        <View style={{ padding: 10, backgroundColor: '#939cb0' }}>
          <Text style={{ color: '#ffffff', textAlign: 'center', fontSize: 24 }}>Audio Player</Text>
        </View>
        <View style={{ alignItems: 'flex-start', flexDirection: 'row', marginTop: 8 }}>
          <View style={statStyle}><Text style={{ textAlign: 'center' }}>Prepared : {(this.state.prepared) ? 'Yes' : 'No'}</Text></View>
          <View style={statStyle}><Text style={{ textAlign: 'center' }}>Playing : {(this.state.playing) ? 'Yes' : 'No'}</Text></View>
          <View style={statStyle}><Text style={{ textAlign: 'center' }}>Duration : {this.state.duration}</Text></View>
        </View>
        <View style={{ padding: 5 }}>
          <TouchableHighlight
            style={buttonStyles}
            onPress={this.playSound.bind(this)}>
            <Text style={{ textAlign: 'center' }}>Play</Text>
          </TouchableHighlight>
          <TouchableHighlight
            style={buttonStyles}
            onPress={this.pauseSound.bind(this)}>
            <Text style={{ textAlign: 'center' }}>Pause</Text>
          </TouchableHighlight>
        </View>
      </View>
    );
  }
}

AppRegistry.registerComponent('ReactNativeModuleTutorial', () => ReactNativeModuleTutorial); 
```

![App Interface](img/6e768988198759742db40d55d8c5ff83.png)

首先导入`NativeModule`的组件，因为它包含刚刚创建的模块和任何其他本机模块。

您使用以下代码访问了该模块:

```
var MyAudioPlayer  = NativeModules.MyAudioPlayer; 
```

现在模块中定义的所有方法都可用了。看一下`componentDidMount`方法，首先使用`MyAudioPlayer.preparePlayer`方法准备播放器，然后为`OnPreparedListener`设置回调方法。

```
componentDidMount(){
  MyAudioPlayer.preparePlayer(this.state.mp3Url);
  MyAudioPlayer.setOnPreparedCallback((duration) => {
    this.setState({ prepared: true, duration: duration });
    ToastAndroid.show('Audio prepared', ToastAndroid.LONG);
  });
} 
```

## 跨平台本机桥

通过创建自己的 React 本机模块，您可以将本机 Android(和 iOS)功能连接到跨平台代码库。您需要了解为您选择的本机代码库编码，但这样做可以帮助 React 本机开发人员获得新的功能和可能性。

你曾经创建过自己的 React 原生模块吗？你做了什么，你觉得这个过程怎么样？

## 分享这篇文章