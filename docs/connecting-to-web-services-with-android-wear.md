# 使用 Android Wear 连接到 Web 服务

> 原文：<https://www.sitepoint.com/connecting-to-web-services-with-android-wear/>

![IoTWeek_Gray](img/defe243339c5bf160966142955834754.png)

SitePoint 的第 10 周！整个星期，我们都在发布关于互联网和物理世界的交集的文章，所以请继续查看[物联网标签](https://www.sitepoint.com/blog/)以获取最新更新。

互联网为我们生活中越来越多的部分提供燃料。我们比以往任何时候都更加紧密地联系在一起，可以快速、几乎无限制地获取信息。我们的手机是这种推动的加速器，从天气预报、新闻、音乐、视频、游戏和其他一切事物中给我们所有我们需要的信息。对连接的渴望催生了可穿戴设备。连接到电话或直接连接到互联网的小型设备，如手表。虽然这些平台仍处于起步阶段，但它们为用户提供了一种新的方式，让他们在最需要的时候获取少量信息。在本教程中，我将展示如何构建一个原生 Android Wear 应用程序，并将其连接到一个 web 服务，以获取最新的潮汐信息。这对于一个可穿戴应用程序来说是完美的，因为你可以快速打开它，连接到 API 并获得你需要的信息的快照。

![App launcher icon](img/51c28b8529d7b52aa94ee4490ff211f1.png)

*启动器图标/颜色由[Elio](https://www.sitepoint.com/author/elio-qoshi/)T3 提供*

你可以在 [GitHub](https://github.com/sitepoint-editors/Android-Wear-API) 上找到最终的项目。

## 构建目标和版本

我用 **Android Studio 2.1** 创建了这个项目，并在手机上针对 **Android 5.0 (API 21)和 Wear** 上针对 **Android 5.0 (API 21)进行了编译。**

因为您将发送数据消息，所以您需要在两个配置文件上使用 [Google Play 服务](https://en.wikipedia.org/wiki/Google_Play_Services)。

**注意**:移动设备和可穿戴设备都需要兼容的 API 版本才能发送和接收数据。移动设备上支持的最低版本是 **Android 4.3 (API 18)** 。

你需要一部实体手机。如果你既有一部手机，又有一块实体 android wear 手表，这个过程会更容易。目前你不能连接一个模拟手机一起观看。

### 考虑和限制

该示例使用 DataAPI 的活动实现让两个配置文件交换信息。这意味着，为了使该过程工作，两个设备需要同时打开。一个更好的选择是使用 [WearableListenerService](https://developer.android.com/training/wearables/data-layer/events.html#Listen) 实现你自己的后台监听器，这样你就可以在两个应用都不打开的情况下交换数据。

在撰写本文时，Android Wear(1.6 版)还不能直接连接互联网。如果您查询穿戴设备的活动连接，它将返回 false。这很有意义，因为从技术上来说，可穿戴设备与互联网没有任何连接，它从其连接的移动设备获得连接。这意味着您需要将所有查询转发到移动设备，执行计算，然后将结果返回给 wear。本教程的大部分内容都致力于建立概要文件之间的连接来交换数据。

当 Android Wear 2.0 发布时，新的蜂窝移动设备很常见，这个过程应该有望更加简化，而不需要从移动设备来回传递信息。

## 流程概述

目标是使用 wear 应用程序连接到一个 API，为用户提供潮汐信息。

以下是该过程的步骤

1.  在可穿戴设备上创建一个启动该过程的按钮。
2.  创建第一条数据消息并将其发送到移动设备，用作启动处理的触发器。
3.  在手机上捕捉信息并开始处理。设置信息，以便它可以连接到 API，构建最终的 URL。
4.  为了不锁定 UI，在后台创建一个异步任务来连接 API。在异步任务中，检查用户是否在线，然后连接到 API 来提取数据。将信息向前传递，并创建一条发送回移动设备的消息。这可能是一条失败消息，也可能是潮汐数据。
5.  在磨损曲线上，收集数据请求并处理它，显示潮汐信息或错误信息。

由于整个过程很复杂，我建议你参考 [GitHub](https://github.com/sitepoint-editors/Android-Wear-API) 上该应用的完整工作副本。由于该项目的复杂性和规模，我将重点放在重要的部分。我跳过了几个步骤，比如设置颜色值、字符串资源和矢量图标，把重点放在核心功能上。

### 将手机连接到物理手表

如果您同时拥有移动设备和穿戴设备，您可以将它们连接在一起进行测试。所有 Android wear 手表都将支持通过蓝牙进行调试。谷歌提供了一个有用的[“通过蓝牙调试”](https://developer.android.com/training/wearables/apps/bt-debugging.html)指南。作为总结，下面是我如何连接我的设备在 Android Studio 中使用

1.  在移动设备上启用 USB 调试(详细步骤请参考上面的调试指南)。
2.  在可穿戴上启用*开发者选项内的蓝牙调试*。
3.  打开手机上的 Android Wear 应用，选择右上角的*设置*。内部设置通过蓝牙启用*调试。*
4.  滚动到 Android Wear 底部，设置*设备调试*为你的可穿戴设备(不是服务器)。
5.  在命令行打开 Android studio 中的*平台工具*目录。您可以通过打开 SDK 管理器并查看 *Android SDK 位置*字段来找到它。例如我的路径是*C:\ Users \ Simon \ AppData \ Local \ Android \ SDK \ platform-tools*
6.  使用命令行，您现在可以访问`adb`命令。键入以下内容

```
adb forward tcp:4444 localabstract:/adb-hub
adb connect localhost:4444 
```

如果连接时出现错误，请尝试使用您的本地 IP 地址。这种情况在我身上发生过几次，对我很有效。

```
adb connect 127.0.0.1:4444 
```

### 将实体手机连接到仿真手表

如果你没有这两个物理设备，你仍然可以使用 Android 模拟器来模拟一个 wear 设备。

一旦你有了你的可穿戴模拟器，你需要把你的手机连接到模拟器上。

1.  通过 USB 将您的手机连接到您的电脑，并在开发者选项中启用 *USB 调试*
2.  导航到*平台工具*目录，这样您就可以访问`adb`命令行工具。
3.  发出命令`adb -d forward tcp:4444 tcp:4444`。这将端口转发您的手机到 Android studio 和连接您的模拟器。几分钟后，你的应用程序和通知应该开始与模拟器同步。

有时，模拟器可能会不稳定，要么不同步所有内容，要么随机断开连接。这应该很少发生，但是如果发生了，重新开始这个过程。

如果你需要更多信息，有[指南帮助你](http://www.tech-recipes.com/rx/49586/how-do-i-connect-an-android-wear-emulator-to-a-real-phone/)使用模拟器。

## 创建新项目

创建一个新的 Android 项目，选择一个名称，并设置您想要的最低手机 SDK。在这个例子中，我为手机和穿戴设备选择了 *Android 5.0* 。

![Setting Targets](img/35d388cacd7bfc7d1168ca218d0cba73.png)

为您的移动布局创建一个空活动，为 wear 创建一个空白活动。Android Wear 会希望你定义方形和圆形布局。暂时保留这些，然后按下*下一个*，但是请注意，您将很快删除这些布局，因为您想要一个布局用于正方形和圆形。

完成所有设置后，您将拥有两个文件夹，一个用于手机，一个用于穿戴，如下所示:

![Project Structure](img/2105231485c0a12ab33416309407ab08.png)

你将在这个项目的移动和穿戴布局上工作。

## 为活动设置数据 api

因为可穿戴设备不能直接访问互联网，所以你需要使用移动配置文件作为中间人。你需要将一个请求从可穿戴设备传递到移动设备，然后移动设备会处理并返回。这是一个简单的需求，但需要协调。

![Application Flow](img/e5d0daa58356bc4fbfc81dc85cbae2ae.png)

所有信息/数据都从设备发送到谷歌的服务器，再重新发送回所有其他“节点”进行更新。

为了让这种通信工作起来，你需要在**的穿戴和移动活动**中实现 [GoogleAPIClient](https://developers.google.com/android/reference/com/google/android/gms/common/api/GoogleApiClient) 类。该类将您的应用程序直接连接到 Google Play 服务，是所有通信的基础。一旦你的应用连接到 Google Play 服务，你就可以通过[数据 Api](https://developers.google.com/android/reference/com/google/android/gms/wearable/DataApi) 发送和接收信息。

### 设置 Google Play 服务

在活动的`onCreate`方法中设置`GoogleApiClient`构建器类。它的目的是创建一个到 Google Play 服务的连接，并在您连接、断开或无法到达服务时将监听器连接在一起。

```
//set up google play services client
googleClient = new GoogleApiClient.Builder(this)
    .addApi(Wearable.API)
    .addConnectionCallbacks(this)
    .addOnConnectionFailedListener(this)
    .build(); 
```

传入构建器对象的`this`组件引用了`MainActivity`类。为了降低复杂性，您将添加接口来将必要的方法组合在一起。

### 设置接口

您现在将[实现接口](https://docs.oracle.com/javase/tutorial/java/concepts/interface.html)。它们可能看起来像一个复杂的过程，但是接口所需要的只是您的活动实现接口的强制方法。这意味着你可以创建一个大型的超类来处理不同的事情。例如连接到 Google Play 服务并监听数据。

实现 [`GoogleApiClient.ConnectionCallbacks`](https://developers.google.com/android/reference/com/google/android/gms/common/api/GoogleApiClient.ConnectionCallbacks) 、 [`GoogleApiClient.OnConnectionFailedListener`](https://developers.google.com/android/reference/com/google/android/gms/common/api/GoogleApiClient.OnConnectionFailedListener) 和 [`DataApi.DataListener`](https://developers.google.com/android/reference/com/google/android/gms/wearable/DataApi.DataListener) 上的活动接口如下:

```
//Mobile Profile activity
public class MainActivity extends AppCompatActivity implements
  DataApi.DataListener,
  GoogleApiClient.ConnectionCallbacks,
  GoogleApiClient.OnConnectionFailedListener{
} 
```

当`GoogleApiClass`对象寻找它的监听器时，它将使用这个类。您必须处理的强制方法是`onConnected`、`onConnectionSuspended`、`onConnectionFailed`和`onDataChanged`方法。

```
//on successful connection to play services, add data listner
public void onConnected(Bundle connectionHint) {
    Wearable.DataApi.addListener(googleClient, this);
}

//on suspended connection, remove play services
public void onConnectionSuspended(int cause) {
     Wearable.DataApi.removeListener(googleClient, this);
}

//On failed connection to play services, remove the data listener
public void onConnectionFailed(ConnectionResult result) {
    Wearable.DataApi.removeListener(googleClient, this);
}
//function triggered every time there's a data change event
public void onDataChanged(DataEventBuffer dataEvents) {
    //To be explained in further detail later
} 
```

一旦你连接到 Google Play 服务，`onConnected`监听器就会启动。既然您已经连接了，那么您希望添加您的侦听器来查找更改的数据。传入`GoogleApiClient`对象和这个类作为它的参数。如果您根本无法连接或断开连接，您需要删除侦听器。如果你与外界没有联系，那么倾听就毫无意义。

### 正在启动 Google Play 服务

现在您已经创建了`GoogleApiClient`对象，您想在活动一出现在屏幕上就连接它，并在活动一消失就删除它。添加`onResume`和`onPause`事件监听器，如下所示:

```
//on resuming activity, connect to play services
public void onResume(){
    super.onResume();
    googleClient.connect();
}

//On leave activity, disconnect from play services and remove data listener
public void onPause(){
    super.onPause();
    Wearable.DataApi.removeListener(googleClient, this);
    googleClient.disconnect();
} 
```

`onResume`方法将调用`connect`方法并尝试连接到 Google Play 服务。如果连接成功，它将触发`onConnected`方法，该方法将设置数据监听器。这将在每次应用程序启动时触发(直接在`onCreate`之后)或返回到屏幕。它可以确保您始终尝试连接到游戏服务。

如果您退出应用程序或导航离开，将调用`onPause`方法。当它被调用时，您想要为 Google Play 服务触发`disconnect`方法。当 play services 断开连接时，它将触发前面创建的`onConnectionSuspended`方法，删除数据监听器。所有这些确保了当应用程序关闭时，你不会浪费时间寻找数据。

### 在设备之间发送数据

通过 [`PutDataMapRequest`](https://developer.android.com/training/wearables/data-layer/data-items.html#SyncData) 和 [`PutDataRequest`](https://developer.android.com/training/wearables/data-layer/data-items.html#SyncData) 类在活动和 Google Play 服务之间发送数据。

创建新的`PutDataMapRequest`请求对象:

```
PutDataMapRequest putDataMapRequest = PutDataMapRequest.create("/apiurl"); 
```

这为资源设置了唯一的 URI，这也是您稍后将检查的内容。

然后通过调用这个对象的`getDataMap`方法，以及添加字符串、整数或其他值的各种`put`方法，将您想要的所有数据添加到这个对象中。

```
putDataMapRequest.getDataMap().putString("message", "This message will go to our mobile layout"); 
```

当所有数据都加载到这个对象中时，创建一个新的`PutDataRequest`对象，并使用`PutDataMapRequest`对象的`asPutDataRequest`方法。这会将数据加载到这个新的网络对象中。

```
PutDataRequest putDataRequest = putDataMapRequest.asPutDataRequest(); 
```

调用`setUrgent`方法以确保数据尽快发送，没有延迟:

```
putDataRequest.setUrgent(); 
```

现在，您可以为数据项创建一个新的`PendingResult`，并使用`DataApi`将消息推送到网络上，供网络上的所有设备接收。

```
PendingResult<DataApi.DataItemResult> pendingResult = Wearable.DataApi.putDataItem(googleClient, putDataRequest); 
```

关于同步数据的谷歌文档很有用，概述了这个过程是如何工作的。

在这个过程中，您将把两条消息推送到网络上。首先是可穿戴开始移动布局和处理的初始消息。第二个是从移动布局开始，从 API 传回潮汐数据。

为了捕捉这些消息，您需要在活动中实现的`onDataChanged`方法中寻找它们。

### 观察数据变化

两个活动都将实现`onDataChanged`方法，这意味着它们都将能够寻找更改的数据/消息。向`onDataChanged`方法传递一组`EventDataBuffer`对象，这些对象代表这个监听器收集的事件。所有监听器收集所有消息，因此您需要搜索它们，并在需要时激活您的功能。

循环通过收集和提取 [`dataItem`](https://developers.google.com/android/reference/com/google/android/gms/wearable/DataItem) 。这些项目可以包含一个名为 [DataMapItem](https://developers.google.com/android/reference/com/google/android/gms/wearable/DataMapItem) 的数据列表。在这个对象中，您可以访问通过 DataApi 传递给 Google Play 服务的字符串、整数和其他值。

**最重要的部分是检查数据项**的`path`值，因为这是它的唯一标识符。这是您查看何时应该触发功能的方式。网络上的每个节点都会收到这些消息，因此您需要使用这种方式来了解何时可以触发您的处理。

在示例中，当您从可穿戴布局接收到数据项时，您希望触发移动布局上的处理。在每个数据事件项中，使用以下方法检查它们是否与字符串`/apiurl`匹配:

```
//check if we received message from wearable
DataItem item = event.getDataItem();
if(item.getUri().getPath().equals("/apiurl")){
    //perform actions here
} 
```

您将在两个概要文件的`MainActvity`类中更详细地查看这一点。

### 概念图

如果所有这些看起来令人困惑，这里有一个图表，展示了这些方法如何相互交互来管理与 Google Play 服务的连接状态和数据监听器的状态。

![Application Logic](img/c918f6dee4c20de836a1f0b43d878f95.png)

## 创建 Android Wear 布局

可穿戴用户界面是用户花费大部分时间的地方。布局包括标题、副标题和触发处理的基本按钮。当你按下按钮时，你触发了一个覆盖 UI，显示一个进度计数器和一个`TextView`来让用户知道正在发生的事情。

![Wearable UI](img/6f75863a8a8a087e1bbde86aa1aef913.png)

*左边是我们的标准布局，右边是活动布局(检索数据时显示)*

当你创建你的项目时，你将在 *res/layout* 文件夹中有一个矩形和圆形布局。删除它们，这样您拥有的就是 *activity_main.xml* 布局文件。打开 *activity_main.xml* 并添加以下内容:

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context="com.example.simon.androidweardatalayer.MainActivity"
    tools:deviceIds="wear">

    <!--Main Layout, displayed on load-->
    <LinearLayout
        android:id="@+id/mainContainer"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:background="#efefef">

        <TextView
            android:id="@+id/title"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:padding="10dp"
            android:textAlignment="center"
            android:textSize="18sp"
            android:text="@string/app_name"
            android:background="@color/color_blue_dark"
            android:textColor="@color/text_light" />

        <TextView
            android:id="@+id/subtitle"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textColor="#333"
            android:textAlignment="center"
            android:textSize="14sp"
            android:text="@string/subtitle"
            android:padding="10dp"/>

        <Button
            android:id="@+id/apiButton"
            android:text="@string/button_text"
            android:layout_centerHorizontal="true"
            android:layout_width="wrap_content"
            android:textAlignment="center"
            android:drawableLeft="@drawable/ic_cloud"
            android:drawablePadding="7dp"
            android:layout_gravity="center"
            android:layout_height="wrap_content"/>

        <LinearLayout
            android:id="@+id/apiContainer"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical">

            <TextView
                android:id="@+id/apiMessage"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textColor="#333"
                android:textAlignment="center"/>

            <TextView
                android:id="@+id/apiDate"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textColor="#333"
                android:textAlignment="center"/>

            <TextView
                android:id="@+id/apiHeight"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textColor="#333"
                android:textAlignment="center"/>
        </LinearLayout>
    </LinearLayout>

    <!--Overlay, displayed only when a user touches the button-->
    <RelativeLayout
        android:id="@+id/overlay"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:background="@color/color_blue_light_trans"
        android:visibility="invisible">

        <ProgressBar
            android:id="@+id/progressBar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:indeterminate="true"
            android:indeterminateTint="@color/text_light"
            android:layout_marginBottom="5dp"/>

        <TextView
            android:id="@+id/progressMessage"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@id/progressBar"
            android:textAlignment="center"
            android:layout_centerHorizontal="true"
            android:text="@string/loading_text"/>

    </RelativeLayout>

</RelativeLayout> 
```

布局是嵌套的，`mainContainer` LinearLayout 位于最前面，而第二个`overlay` RelativeLayout 容器设置在后台。这个覆盖图最初是不可见的，当用户点击按钮时，切换这个覆盖图可见，这样用户就不能选择任何东西，直到请求完成。

## 可穿戴主要活动

为您的可穿戴个人资料打开`mainActivity`类。这个概要文件的主要职责是处理启动流程的 click 事件，以及 UI 的交互，比如显示覆盖容器和最终更新 UI 以显示 tidal 信息。

```
//Wearable Layout
public class MainActivity extends Activity implements
        DataApi.DataListener, GoogleApiClient.ConnectionCallbacks, GoogleApiClient.OnConnectionFailedListener{

    private GoogleApiClient googleClient;

    private LinearLayout mainContainer;
    private TextView apiMessage;
    private TextView apiDate;
    private TextView apiHeight;
    private RelativeLayout overlay;
    private Button apiButton;

    //on successful connection to play services, add data listner
    public void onConnected(Bundle connectionHint) {
        Wearable.DataApi.addListener(googleClient, this);
    }

    //on resuming activity, reconnect play services
    public void onResume(){
        super.onResume();
        googleClient.connect();
    }

    //on suspended connection, remove play services
    public void onConnectionSuspended(int cause) {
        Wearable.DataApi.removeListener(googleClient, this);
    }

    //pause listener, disconnect play services
    public void onPause(){
        super.onPause();
        Wearable.DataApi.removeListener(googleClient, this);
        googleClient.disconnect();
    }

    //On failed connection to play services, remove the data listener
    public void onConnectionFailed(ConnectionResult result) {
        Wearable.DataApi.removeListener(googleClient, this);
    }

    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);

        //set up google play services client
        googleClient = new GoogleApiClient.Builder(this)
                .addApi(Wearable.API)
                .addConnectionCallbacks(this)
                .addOnConnectionFailedListener(this)
                .build();

        //find all UI elements
        apiMessage = (TextView) findViewById(R.id.apiMessage);
        apiDate = (TextView) findViewById(R.id.apiDate);
        apiHeight = (TextView) findViewById(R.id.apiHeight);
        mainContainer = (LinearLayout) findViewById(R.id.mainContainer);
        overlay =  (RelativeLayout) findViewById(R.id.overlay);
        apiButton = (Button) findViewById(R.id.apiButton);

        //click action for button, connect to mobile
        apiButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {

                //bring the loading overlay to the front
                overlay.setVisibility(View.VISIBLE);
                overlay.bringToFront();

                //start API request to phone
                PutDataMapRequest putDataMapRequest = PutDataMapRequest.create("/apiurl");
                putDataMapRequest.getDataMap().putString("message", "This is a message from Android Wear, connect to the API");
                putDataMapRequest.getDataMap().putLong("time", new Date().getTime());
                PutDataRequest putDataRequest = putDataMapRequest.asPutDataRequest();
                putDataRequest.setUrgent();

                PendingResult<DataApi.DataItemResult> pendingResult = Wearable.DataApi.putDataItem(googleClient, putDataRequest);
            }
        });

        //click listenr for the overlay, touch to dismiss it in case the API fails or takes too long
        overlay.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                overlay.setVisibility(View.INVISIBLE);
                mainContainer.bringToFront();
            }
        });
    }

    //function triggered every time there's a data change event
    public void onDataChanged(DataEventBuffer dataEvents) {
        for(DataEvent event: dataEvents){

            //data item changed
            if(event.getType() == DataEvent.TYPE_CHANGED){

                DataItem item = event.getDataItem();
                DataMapItem dataMapItem = DataMapItem.fromDataItem(item);

                //RESPONSE back from mobile message
                if(item.getUri().getPath().equals("/responsemessage")){

                    //received a response back, turn overlay off and bring main content back to front
                    overlay.setVisibility(View.INVISIBLE);
                    mainContainer.bringToFront();

                    //collect all info
                    String error = dataMapItem.getDataMap().getString("error");
                    String unixTime = dataMapItem.getDataMap().getString("unixTime");
                    String height = dataMapItem.getDataMap().getString("height");

                    //success
                    if(error == null){
                        apiMessage.setText("Current Time Info");
                        apiDate.setText("Date|Time: " + unixTime);
                        apiHeight.setText("Height: "+ height + " Meters");
                    }
                    //error
                    else {
                        apiMessage.setText(error);
                    }

                }
            }
        }
    }

} 
```

这里需要关注几个要素:

*   按钮的 click 事件处理程序启动了数据连接过程。当按下时，它将覆盖图带到前台，创建一个数据项(带有请求消息)，然后将其传递给 Google Play 服务。
*   `onDataChanged`方法在完成时监听从移动设备发送的返回数据。收到信息后，禁用叠加，更新可穿戴设备 UI，显示最新的潮汐信息。

接下来更新磨损配置文件的 *AndroidManifest.xml* 文件，在`<activity>`中添加`com.google.android.gms.version`元标签。

```
<meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" /> 
```

## 移动布局

手机简介并不令人兴奋。它唯一的元素是视图顶部的一个`TextView`组件，作为 API 消息的容器。一旦手机从可穿戴设备接收到数据事件，它就会将初始消息传递给手机，以在容器中显示它。

![Mobile UI](img/5474938031933f47ca7b99825aac7c20.png)

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.example.simon.androidweardatalayer.MainActivity">

    <TextView
        android:id="@+id/messageContainer"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="No Message yet"/>

</RelativeLayout> 
```

## 移动主要活动

移动配置文件中的`MainActivity`类是发生繁重工作的地方。由于可穿戴设备的限制，连接 API 并提取信息取决于移动设备。`MainActivity`类类似于可穿戴活动，连接 DataApi 和 Google Play 服务。它还附加了其他类和方法来准备、连接和处理来自 API 的结果。

在过程的最后，移动布局进行处理，可穿戴设备应该接收回它的数据并更新它的显示。

```
//Mobile Profile
public class MainActivity extends AppCompatActivity implements
        DataApi.DataListener, GoogleApiClient.ConnectionCallbacks, GoogleApiClient.OnConnectionFailedListener{

    private Activity activity;
    private GoogleApiClient googleClient;
    private TextView messageContainer;

    //on successful connection to play services, add data listner
    public void onConnected(Bundle connectionHint) {
        Wearable.DataApi.addListener(googleClient, this);
    }

    //on resuming activity, reconnect play services
    public void onResume(){
        super.onResume();
        googleClient.connect();
    }

    //on suspended connection, remove play services
    public void onConnectionSuspended(int cause) {
        Wearable.DataApi.removeListener(googleClient, this);
    }

    //pause listener, disconnect play services
    public void onPause(){
        super.onPause();
        Wearable.DataApi.removeListener(googleClient, this);
        googleClient.disconnect();
    }

    //On failed connection to play services, remove the data listener
    public void onConnectionFailed(ConnectionResult result) {
        Wearable.DataApi.removeListener(googleClient, this);
    }

    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        this.activity = this;

        //data layer
        googleClient = new GoogleApiClient.Builder(this)
                .addApi(Wearable.API)
                .addConnectionCallbacks(this)
                .addOnConnectionFailedListener(this)
                .build();

        messageContainer = (TextView) findViewById(R.id.messageContainer);

    }

    //watches for data item
    public void onDataChanged(DataEventBuffer dataEvents) {
        for(DataEvent event: dataEvents){

            //data item changed
            if(event.getType() == DataEvent.TYPE_CHANGED){

                DataItem item = event.getDataItem();
                DataMapItem dataMapItem = DataMapItem.fromDataItem(item);

                if(item.getUri().getPath().equals("/apiurl")){

                    Log.d("debug", "caught message passed to me by the wearable");

                    String message = dataMapItem.getDataMap().getString("message");

                    Log.d("debug", "here is the message: " + message);
                    messageContainer.setText(message);

                    //BUILD API ARGUMENTS
                    //populate API information, in preparation for API call
                    APIInformation apiInformation = setUpAPIInformation();

                    //EXECUTE ASYNC TASK
                    APIAsyncTask asyncTask = new APIAsyncTask();
                    asyncTask.execute(apiInformation);

                }
            }
        }
    }
    //`isOnline`,`setUpAPIInformation` methods along with the `APIAsyncTask` class to go in here 
} 
```

`onDataChanged`方法是您开始大部分处理的地方。当你选择一个带有`/apiurl` ID 的数据项时，开始这个过程。

### 测试与互联网的连接

在连接到 API 之前，您需要知道是否有可用的互联网。为了测试这一点，在`MainActivity`中创建一个`isOnline`方法。

```
//checks to see if we are online
protected boolean isOnline(){
    ConnectivityManager connectivityManager = (ConnectivityManager) activity.getSystemService(Context.CONNECTIVITY_SERVICE);
    NetworkInfo networkInfo = connectivityManager.getActiveNetworkInfo();

    boolean connected = false;
    if((networkInfo != null) && (networkInfo.isConnected())){
        connected = true;
    }

    return connected;
} 
```

从活动中的`getSystemService`方法获取 [`ConnectivityManager`](https://developer.android.com/reference/android/net/ConnectivityManager.html) 对象。您请求连接服务，以便查询您拥有什么访问权限。

从连接服务创建一个 [`NetworkInfo`](https://developer.android.com/reference/android/net/NetworkInfo.html) 对象，检查你是否连接，返回真或假。

如果没有连接，就不要连接到 API。

**注意**:确保你有正确的权限访问网络状态和互联网。打开移动配置文件的 *AndroidManifest.xml* 文件，并确保在`<application>`标记之前拥有以下权限。

```
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/> 
```

### 首选 API 世界潮汐

要获取潮汐数据，你需要一个好的 API，而世界潮汐网站是完美的。你想要的只是最新的潮汐高度和日期，API 提供了[定制选项来配置](https://www.worldtides.info/apidocs)。

![Tides API](img/1e5a03270b2f482d69329caa6a71cd7f.png)

API 需要的只是一个直接的 URL，它将返回一个 JSON 编码的值字符串。例如，要获得一个最新的潮汐读数，您可以使用以下端点

```
https://www.worldtides.info/api?heights⪫=34.057&lon=151.152&length=21600&step=21600&key=af967f62-eb62-4574-b75e-a9056859055e 
```

你只需要一个电流值就可以回到可穿戴设备。

### 为 API 调用和填充数据做准备

在连接到 API 之前，您需要构建最终端点，加载 URL 以收集数据。为了使流程更干净，添加一个名为`APIInformation`的新类，它将处理属性的设置和获取。

```
/*Class that holds information for API call*/
public class APIInformation {

   private String APIEndpoint;
   private Map<String, String> APIArguments = new HashMap<>();
   private String APIUrl;

   //endpoint (main url to the target API)
   public void setAPIEndpoint(String endpoint){
       this.APIEndpoint = endpoint;
   }
   public String getAPIEndpoint(){
       return APIEndpoint;
   }

   //set a single API argument to the arg list
   public void setAPIArgument(String key, String value){
       this.APIArguments.put(key, value);
   }
   //gets a single API argument from the arg list
   public String getAPIArgument(String key){
       String value = APIArguments.get(key);
       return value;
   }

   //set a bunch of API arguments to the arg list
   public void setAPIArguments(HashMap<String, String> arguments){
       this.APIArguments = arguments;
   }

   //gets all the API arguments as a HashMap
   public Map<String, String> getAPIArguments(){
       return APIArguments;
   }

   //creates the final API url based on endpoint, key and arguments
   public void setAPIUrl(){
       StringBuilder builder = new StringBuilder();

       builder.append(this.APIEndpoint);

       //loop through all arguments
       Map<String,String> arguments = this.getAPIArguments();
       if(arguments.size() != 0){

           Integer counter = 1;
           builder.append("?");
           for ( Map.Entry<String, String> entry : arguments.entrySet()){
               String key = entry.getKey();
               String value = entry.getValue();

               //check for empty keys or values (some arguments don't need values passed)
               if(value.isEmpty() && !key.isEmpty()){
                   builder.append(key);
               }else{
                   builder.append(key + "=" + value);
               }

               if(counter != arguments.size()){
                   builder.append("&");
               }

               counter++;
           }
       }

       this.APIUrl = builder.toString();
   }

   //gets the final API url to call
   public String getAPIUrl(){
       return this.APIUrl;
   }
} 
```

该类处理最终 API 端点的创建，方法是将所有传递的参数加在一起，并将它们与 API 基相结合。一种更简单的方法是硬编码最终的 URL，以绕过对另一个类的需求。虽然这是可行的，但是单独的类的目的是创建信息集来执行一系列到 API 的其他连接。例如，您可能希望收集所有最近的报告站，然后获取它们某一天的预测潮汐高度。

例如，在`MainActivity`中创建一个名为`setUpAPIInformation`的新方法，它将填充一个新的`APIInformation`对象。

```
//populates information for API
protected APIInformation setUpAPIInformation(){

    APIInformation apiInformation = new APIInformation();

    apiInformation.setAPIEndpoint("http://www.worldtides.info/api");
    HashMap arguments = new HashMap<String, String>();

    arguments.put("key", "1d3d0a79-5d7d-48d3-9e80-a5383e53eba2");
    arguments.put("heights",""); //we want the heights only
    arguments.put("lat", "-34.057440"); //cronulla sydney
    arguments.put("lon", "151.152190"); //cronulla sydney
    arguments.put("step", "21600"); //6 hours
    arguments.put("length", "21600"); //6 hours

    //determine the next time period (after right now)
    Long currentTime = System.currentTimeMillis();
    String time = String.valueOf(currentTime / 1000L);
    arguments.put("start", time);

    apiInformation.setAPIArguments(arguments);
    apiInformation.setAPIUrl();

    return apiInformation;
} 
```

更好的是，通过将当前系统日期填充到 API 中，您可以获得下一个潮汐周期。

一旦您准备好所有这些设置，在自定义`AsyncTask`中执行它，以连接到 API 并获取信息。

### 使用异步任务连接到 API

为了不锁定 UI，您需要在后台线程中执行 API 请求。

您可以使用 [`AsyncTask`](https://developer.android.com/reference/android/os/AsyncTask.html) 类在后台线程中执行任务。你不直接使用`AsyncTask`，而是扩展自己的类。为了简化这个过程，创建一个`APIAsyncTask`类作为`MainActivity`的内部类，这样可以方便地访问 UI 进行更新。

讨论`AsyncTask`完全是一个完整的话题，但其核心是三个重要的方法:

*   `OnPreExecute`:本例中未使用
*   `doInBackground`:执行后台线程，连接 API，检索信息进行处理。
*   `OnPostExecute`:后台任务完成后执行。在这里，您从 API 收集数据，并用 DataApi 形成响应消息，并将其推送到 Google Play 网络，供可穿戴设备收集。

```
//main async task to connect to API and collect a response
public class APIAsyncTask extends AsyncTask<APIInformation, String, HashMap> {

    //execute before we start
    protected void onPreExecute() {
        super.onPreExecute();
    }

    //execute background task
    protected HashMap doInBackground(APIInformation... params) {

        APIInformation apiInformation = params[0];
        boolean isOnline = isOnline();
        HashMap result;

        if(isOnline()){

            //perform a HTTP request
            APIUrlConnection apiUrlConnection = new APIUrlConnection();

            //get the result back and process
            result = apiUrlConnection.GetData(apiInformation.getAPIUrl());

        }else{
            //we're not online, flag the error
            Log.d("debug", "Error, not currently online, cant connect to API");
            result = new HashMap();
            result.put("type", "failure");
            result.put("data", "Not currrently online, can't connect to API");
        }
        return result;
    }

    //update progress
    protected void onProgressUpdate(String... values) {
        super.onProgressUpdate(values);
    }

    //Execute once we're done
    protected void onPostExecute(HashMap result) {
        super.onPostExecute(result);

        //build message back to the wearable (either with data or a failure message)
        PutDataMapRequest putDataMapRequest = PutDataMapRequest.create("/responsemessage");
        putDataMapRequest.getDataMap().putLong("time", new Date().getTime());

        //success (we collected data from the API)
        if(result.get("type") == "success"){
            //get the json response data string
            String data = (String) result.get("data");

            //create a new json object
            try{
                JSONObject jsonObject = new JSONObject(data);
                if(jsonObject.has("heights")){

                    JSONArray heights = (JSONArray) jsonObject.get("heights");
                    //loop through all 'heights' objects to get data

                    for(int i = 0; i < heights.length(); i++){
                        JSONObject heightObject = heights.getJSONObject(i);
                        Integer unixTime = Integer.parseInt(heightObject.getString("dt"));
                        String height = heightObject.getString("height");

                        //need to process the values to make them readable.
                        String heightTrimmed = height.substring(0, 5);

                        //convert date unix string to a human readable format
                        Date date = new Date(unixTime * 1000L);
                        DateFormat format = new SimpleDateFormat("dd/MM/yyyy hh:mm a");
                        String dateFormatted = format.format(date);

                        //add data to be passed back to the wearable
                        putDataMapRequest.getDataMap().putString("unixTime", dateFormatted);
                        putDataMapRequest.getDataMap().putString("height", heightTrimmed);
                    }
                }else{
                    Log.d("error", "there was no height parm returned from the API");
                    putDataMapRequest.getDataMap().putString("error", "There was an issue processing the JSON object returned from API");
                }

            }catch(Exception e){
                Log.d("error", "error creating the json object: " + e.getMessage());
                putDataMapRequest.getDataMap().putString("error", "There was an issue processing the JSON object returned from API");
            }

        }
        //failure (couldn't connect to the API or collect data)
        else if(result.get("type") == "failure"){
            Log.d("error", "There was an issue connecting to the API.");
            putDataMapRequest.getDataMap().putString("error", result.get("error").toString());
        }

        //finalise message and send it off (either success or failure)
        PutDataRequest putDataRequest = putDataMapRequest.asPutDataRequest();
        putDataRequest.setUrgent();
        PendingResult<DataApi.DataItemResult> pendingResult = Wearable.DataApi.putDataItem(googleClient, putDataRequest);
    }
} 
```

在`doInBackgroundTask`方法中，使用`APIUrlConnection`类连接到 API 的最终端点。

```
APIUrlConnection apiUrlConnection = new APIUrlConnection();
result = apiUrlConnection.GetData(apiInformation.getAPIUrl()); 
```

收集信息或错误信息，并将其传递给`onPreExecute`方法进行处理。

当你从`GetData`方法返回时，你有一个带有两个键的[散列表](https://docs.oracle.com/javase/7/docs/api/java/util/HashMap.html)。第一个键`type`，代表我们连接到 API 是成功还是失败。第二个键`data`，包含错误消息或返回内容的长 JSON 编码字符串，以便进一步细化。

#### 使用 HttpUrlConnection 获取 API 响应

一旦有了最终的 API 端点，就可以开始连接了。为了连接 web 服务，Android 提供了 [`HttpUrlConnection`](https://developer.android.com/reference/java/net/HttpURLConnection.html) 类结合 [`BufferedReader`](https://docs.oracle.com/javase/7/docs/api/java/io/BufferedReader.html) 对象来拉取数据。这个过程将从 API 中一个字符一个字符、一行一行地获取结果，直到不再需要读取结果。一旦获得了所有信息，就可以将其传递给`AsyncTask`的`onPostExecute`方法进行处理并转换成 JSON 对象。

```
/*takes an API request (url) and performs it*/
public class APIUrlConnection {

  //gets data based on URL, passed back something
  public HashMap GetData(String url){

      HashMap result = new HashMap<String, String>();
      BufferedReader bufferedReader = null;
      try{
          //use the URL to create a new connection and read content
          URL APIUrl = new URL(url);
          HttpURLConnection httpURLConnection = (HttpURLConnection) APIUrl.openConnection();

          StringBuilder stringBuilder = new StringBuilder();
          bufferedReader = new BufferedReader(new InputStreamReader(httpURLConnection.getInputStream()));
          int responseCode = httpURLConnection.getResponseCode();
          String responseMessage = httpURLConnection.getResponseMessage();

          String line;
          while ((line = bufferedReader.readLine()) != null){
              stringBuilder.append(line);
          }

          //add back result data
          result.put("type", "success");
          result.put("data", stringBuilder.toString());

      }catch(Exception e){

          e.printStackTrace();
          //return error
          result.put("type", "failure");
          result.put("data", "there was an error reading in data from the API: " + e.getMessage());

      }finally {
          //close input steam and finish
          if(bufferedReader != null){
              try{
                  bufferedReader.close();
              }
              catch(Exception e){
                  e.printStackTrace();
              }
          }
      }

      return result;
  }
} 
```

#### 将结果转换成 JSON 并进行处理

下一步是将 JSON 字符串转换成对象。用 [`JSONObect`](https://developer.android.com/reference/org/json/JSONObject.html) 将顶层元素铸造成一个对象。因为这个对象中可能有几组返回的信息，所以您需要将子结果转换成一个 [`JSONArray`](https://developer.android.com/reference/org/json/JSONArray.html) 。然后遍历每个结果来提取潮汐信息。

```
 try{
     JSONObject jsonObject = new JSONObject(data);
     if(jsonObject.has("heights")){

          JSONArray heights = (JSONArray) jsonObject.get("heights");

          //loop through all 'heights' objects to get data
          for(int i = 0; i < heights.length(); i++){
              //get the specific object from the set
              JSONObject heightObject = heights.getJSONObject(i);
              //get time and height values
              Integer unixTime = Integer.parseInt(heightObject.getString("dt"));
              String height = heightObject.getString("height");
           }
      }
}catch(Exception e){
    //error processing
} 
```

从这一点来看，你是在直接处理 API 源代码。根据 API 提供的内容，这个过程会有明显的不同。一般来说，他们会提供关于响应的文档，这样你就可以知道如何处理数据。

在这种情况下，您正在收集下一波的日期、时间和高度信息。给你的是原始 Unix 格式的日期和长整数形式的高度，所以你需要把它们都提炼成有用的东西，以便传回给可穿戴设备。

#### 将信息传回可穿戴设备

一旦你转换了信息，将它们添加到`putDataMapRequest`对象中，准备发送回可穿戴设备。你应该有一个日期字符串和潮汐期的海拔高度。这两者都将通过 DataApi 和`putString`方法传递给可穿戴设备

```
//add data to be passed back to the wearable
putDataMapRequest.getDataMap().putString("unixTime", dateFormatted);
putDataMapRequest.getDataMap().putString("height", heightTrimmed); 
```

现在发送准备好从可穿戴设备收集的数据项。

```
//finalise message and send it off (either success or failure)
PutDataRequest putDataRequest = putDataMapRequest.asPutDataRequest();
putDataRequest.setUrgent();
PendingResult<DataApi.DataItemResult> pendingResult = Wearable.DataApi.putDataItem(googleClient, putDataRequest); 
```

## Android Wear 的后续步骤

随着平台的发展，连接网络服务和处理微数据将成为可穿戴体验的核心部分。随着越来越多的人接受可穿戴设备和物联网，一系列伟大的新硬件设备将会出现，充分利用互联网。

虽然该应用程序只关注提取潮汐数据，但您可以将这些概念应用于任何 web 服务。你可能想要建立一个连接到时刻表信息的应用程序来计划和协调你的火车时刻表(通过通知和振动来确保你不会再次错过火车)。可穿戴设备的优势在于提供最基本的东西，这意味着用户通常只需点击一下鼠标就能获得数据。

如果你对 Android Wear 开发感兴趣，有几种方法可以扩展我在本教程中使用的想法。

*   为移动和穿戴布局实现监听器服务，以便不需要打开任何一个来同步数据。
*   拉入一系列内容，并提供一个可滚动的信息列表。例如，用简单的图表表示潮汐周期的范围。
*   加载时预取信息。您可以存储您的结果，这样您就可以离线访问它们，而不必连接到手机来获取新信息。
*   利用扩展通知和可穿戴特定功能，使您的应用程序更具交互性。

*如果你有任何问题或意见，我希望在下面听到它们*。

## 分享这篇文章