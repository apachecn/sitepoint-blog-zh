# Microsoft Band SDK 入门

> 原文：<https://www.sitepoint.com/getting-started-with-microsoft-band-sdk/>

2014 年 10 月，微软发布了“微软手环”——一款智能手表/健身手环。对它的接受甚至让微软感到惊讶，在准备了有限数量的设备后，发现需求远远超过供应。不久之后，他们发布了 Band SDK，允许开发人员针对乐队，提供应用程序来陪伴乐队或扩展其体验。在首次发布一年后，Band 2 发布了，改进了设备的几个方面，并采用了新的更时尚、弯曲的设计。

![Microsoft Band 2](img/0e60ac1209d0dc664dfcecc3489b6a06.png)

微软 Band 2(图片来源:微软)

这款手环充满了传感器，专注于健身和体育活动中的使用——但它也可以用作睡眠追踪器和智能手表，显示来自配对手机和/或脸书和 Twitter 等社交网络的通知。

## Band SDK 特性

在我们深入研究 SDK 的功能之前，了解任何用户/SDK 代码都将*而不是*在表带本身上运行(这与 Apple Watch 使用的模式类似)，而是在与表带配对的设备上运行，这一点至关重要。

该设备可以运行 Band SDK 支持的任何平台——iOS、Android 和 Windows(包括 Windows 10 和 Windows 8.1 应用)。

然而，当您利用 SDK 时，它将为您提供丰富的功能，包括访问其传感器数据，提供在波段上显示的自定义 UI(称为“tiles”和“tiles content”)，以及向波段发送通知。

Microsoft Band SDK 提供的功能如下:

*   多平台支持
*   传感器数据订阅
*   图块创建和管理
*   磁贴通知
*   自定义布局
*   触觉通知
*   乐队主题个性化

### 带状传感器

正如我们之前提到的，这个带子上布满了传感器。以下是由 Band/Band 2 提供的完整传感器列表:

*   **加速度计—** 测量 X、Y、Z 轴上的加速度。
*   **陀螺仪—** 测量角速度。
*   距离—以厘米为单位测量距离，并提供当前速度。
*   **心率—** 测量佩戴者每分钟的心跳次数。
*   **计步器—** 测量自表带上次出厂复位后的步数。
*   **皮肤温度—** 测量穿着者的皮肤温度，单位为摄氏度。
*   **UV —** 测量当前的紫外线照射强度。
*   **卡路里—** 测量穿戴者消耗的卡路里总量。
*   **皮肤电反应* —** 提供佩戴者的皮肤电阻。
*   **RR 间期* —** 提供最后两次连续心跳之间的间隔。
*   **环境光线* —** 提供当前室内光线强度。
*   **气压计* —** 提供当前原始气压。
*   **高度计* —** 提供当前高度。

**仅限微软 Band 2。*

更多信息可在[微软腕带传感器页面](https://www.microsoft.com/microsoft-band/en-us/support/hardware/sensors)上找到。

在本文中，我们将了解如何使用所有三大平台(iOS、Android 和 Windows)读取传感器数据。首先，我们需要设置我们的应用程序来使用 SDK。

在[微软 Band 平台页面](https://developer.microsoftband.com/bandSDK)下载与所选平台相关的 SDK。如果您的目标是创建一个 UWP(通用 Windows 平台)应用程序，您可以使用 Nuget 通过[微软 Band nuget 包](https://www.nuget.org/packages/Microsoft.Band/)下载并安装 SDK 到我们的应用程序中。

先说 iOS。

## iOS–设置 Band SDK 环境

目前 iOS 7 及以上版本支持 Band SDK。对 iOS 模拟器的支持是不可用的，所以我们需要在与乐队配对的真实设备上测试我们的应用程序。

如果我们希望我们的应用程序在后台打开时保持与表带的通信，我们需要在后台模式下启用“使用蓝牙 le 配件”。为了这个教程，就没必要了。

*   启动 Xcode(版本 6.0 及以上)并创建一个新的单页 iPhone 应用程序。本文将使用 Objective-C，但是将示例代码翻译成 Swift 相当简单。
*   首先，我们需要将 Band SDK 添加到我们的项目中。在项目导航器中，右键单击我们的项目名称并选择*“添加文件…”*。
*   找到并选择 MicrosoftBandKit 框架文件(从您下载并解压缩 SDK 文件的位置)，并选择选项*“如果需要，复制项目”*。
*   选择项目目标下的*“构建阶段”*。并在*【链接二进制与库】*下添加 *CoreBluetooth.framework* 。

现在，我们准备开始编码。

## iOS —连接到波段设备

导航到 ViewController.m 文件。添加 import 语句，以便访问 Micorsoft Band SDK。

```
#import <MicrosoftBandKit_iOS/MicrosoftBandKit_iOS.h> 
```

让我们向 ViewController 定义添加一个委托。通过这个委托，当我们成功地连接或断开设备时，我们将得到回调。

在 ViewController 的实现部分中添加以下回调:

```
@interface ViewController () <MSBClientManagerDelegate>
....
@implementation ViewController

-(void)clientManager:(MSBClientManager *)cm
  clientDidConnect:(MSBClient *)client {
  // handle connected event
}
-(void)clientManager:(MSBClientManager *)cm
clientDidDisconnect:(MSBClient *)client {
  // handle disconnected event
}
-(void)clientManager:(MSBClientManager *)cm client:(MSBClient *)client
didFailToConnectWithError:(NSError *)error {
  // handle failure event
} 
```

稍后，我们将尝试连接一个 Band 设备，我们将把这个实例存储在一个属性中，以便我们能够通过各种方法访问它。

```
@interface ViewController () <MSBClientManagerDelegate>
@property (nonatomic, weak) MSBClient *client;
@end 
```

本教程假设读者对 iOS 开发足够熟悉，并且创建一个简单的带有按钮和标签的 UI 是轻而易举的事情。为了使本教程尽可能简单，我们将不详细讨论如何创建用户界面以及如何将它与 ViewController 端的实例和动作联系起来。现在添加一个按钮，并将其与名为 init 的操作挂钩。

`MSBClientManager`是一个单例，它将为我们提供对 SDK 功能的访问。稍后，我们将使用设备客户端的一个实例来访问传感器、图块和 SDK 的其他功能。在这个方法中，我们将尝试获取那个实例。

正如承诺的那样，我们正在设置我们的`ViewController`，以便在调用`setDelegate`时通过代理发出通知。

然后我们正在访问`attachedClients`集合。这将为我们提供与运行此应用程序的设备配对的所有 Band 设备的列表。*(是的，单个设备可以连接多个频段！)*。你的代码可以有启发式/用户界面来决定连接到哪个波段，在本教程中，我们将只抓住第一个。

最后，我们通过调用`connectClient`并传递 Band 的客户端实例来发起一个调用，尝试连接到这个设备。

```
- (IBAction)init:(id)sender {
  [[MSBClientManager sharedManager] setDelegate:self];
  NSArray *attachedClients = [[MSBClientManager sharedManager]
                              attachedClients];
  self.client = [attachedClients firstObject];
  if (self.client) {
      [[MSBClientManager sharedManager] connectClient:self.client];
  }
} 
```

## iOS–访问传感器

假设一切顺利，我们已经成功连接到一个波段，我们现在可以开始访问传感器的数据馈送。然而，一些传感器需要明确的用户同意。目前，只有心率和 RR 间期传感器需要同意，但这可能会在没有警告的情况下改变。当您需要访问传感器时，最好检查同意情况。

我再次假设您的视图中有另一个按钮可以调用下面的方法。

首先，我们检查我们是否已经获得用户对传感器的同意。如前所述，由于在本例中，我们要求访问心率传感器，因此很可能会收到`MSBUserConsentNotSpecified`。我们需要呼叫`requestHRUserConsentWithCompletion`。设备上将弹出一个对话框，用户可以在其中批准我们的应用程序访问他们的心率。

![Heart rate consent on iOS](img/6ce1340bb4ffab48047d732bfbefa280.png)

iOS 上的同意对话框

对这个方法的连续调用现在应该给出一个`MSBUserConsentGranted`或`MSBUserConsentDeclined`。出于本教程的考虑，让我们假设乐观的场景已经发生，并且用户已经同意。我们现在可以称这个方法为`startHeartRateUpdates`,我们很快就会描述它。

```
- (IBAction)getSensor:(id)sender {
  MSBUserConsent consent = [self.client.sensorManager
                            heartRateUserConsent];
  switch (consent) {
    case MSBUserConsentGranted:
      // user has granted access
      [self startHeartRateUpdates];
      break;
    case MSBUserConsentNotSpecified:
      // request user consent
      [self.client.sensorManager
          requestHRUserConsentWithCompletion:
               ^(BOOL userConsent, NSError *error) {
          if (userConsent) {
              // user granted access
          }
          else {
              // user declined access
          }
      }];
      break;
    case MSBUserConsentDeclined:
      // user has declined access
      break;
    default:
      break;
  }
} 
```

为了接收传感器更新，我们将调用`startHeartRateUpdatesToQueue`。一旦成功，它将按照为每个传感器预先配置的时间间隔调用回调参数(在 Objective-C 中称为“块”)(有关每个传感器更新时间间隔的更多信息，请查阅文档)。在块内，你可以随意处理数据。出于本教程的目的，我们将用回调参数提供的信息更新 UI。对于每个呼叫，我们将获得一个代表当前传感器数据的值，在本例中，是心率和质量指标，这将有助于我们了解该信息的可靠性。具体来说，对于心率，如果质量参数未锁定，您应该怀疑样本的有效性。

```
- (void)startHeartRateUpdates {
  NSError *subscriptionError;
  [self.client.sensorManager startHeartRateUpdatesToQueue:nil errorRef:&subscriptionError withHandler:^(MSBSensorHeartRateData *heartRateData, NSError *error) {
    if (error) {
      // handle error
    }
    _heartRateLabel.text =
    [NSString stringWithFormat:@"Heart Rate: %3u %@",
     (unsigned int)heartRateData.heartRate,
     heartRateData.quality == MSBSensorHeartRateQualityAcquiring ? @"Acquiring" : @"Locked"];
  }];
  if (subscriptionError) {
    // failed to subscribe
  }
} 
```

一旦我们完成了对传感器的访问，取消订阅传感器被认为是一种良好的行为。这一部分很简单，只需调用`stopHeartRateUpdatesErrorRef`就可以了。

```
- (IBAction)stop:(id)sender {
  [self.client.sensorManager stopHeartRateUpdatesErrorRef:nil];
} 
```

通过几行简单的代码，我们展示了如何从头开始创建一个 iOS 应用程序，连接腕带并从中接收心率数据。

由于 Band SDK 是多平台的，所以 Android 和 Windows 的代码在概念上是相似的。让我们来看看 Android。

## Android–设置 Band SDK 环境

目前 Android API 17 及以上支持 Band SDK。

Android SDK 依赖于微软健康应用程序提供的功能。要在 Android 上使用 SDK，需要在手机上安装微软健康应用程序。如果手机上没有安装 Microsoft Health 应用程序，任何创建 Microsoft Band 客户端的尝试都将失败并出现错误。

任何 Android 应用都应该在其 *AndroidManifest.xml* 中提供以下权限:

```
<uses -permission android:name="android.permission.BLUETOOTH"></uses>

<uses -permission android:name="com.microsoft.band.service.access.BIND_BAND_SERVICE"></uses>
```

首先，启动 Android Studio(或您最喜欢的 Android IDE)并创建一个基本的 Android 应用程序。

我们需要在我们的项目中添加 Band SDK。进入 Android 项目的应用目录，找到 *libs* 文件夹(如果不存在可以创建)，复制 *microsoft-band-@version@。jar* 文件到这个位置。

右键单击 app 模块(在项目结构窗口中)，选择“打开模块设置”。选择“依赖项”选项卡，然后单击“+”按钮添加“文件依赖项”。您应该在 *libs* 文件夹中看到我们之前复制的 jar 文件。选择它并点击*确定*。

将上述权限添加到 *AndroidManifest.xml* 中。

现在，我们准备开始编码。

## Android —连接到 Band 设备

导航到活动文件并添加以下导入语句，以便访问 Microsoft Band SDK:

```
import com.microsoft.band.BandClient;
import com.microsoft.band.BandClientManager;
import com.microsoft.band.BandException;
import com.microsoft.band.BandInfo;
import com.microsoft.band.BandIOException;
import com.microsoft.band.ConnectionState;
import com.microsoft.band.sensors.BandHeartRateEvent;
import com.microsoft.band.sensors.BandHeartRateEventListener;
import com.microsoft.band.sensors.HeartRateConsentListener; 
```

必须通过后台线程连接到乐队。我们将使用 Android 的 AsyncTask 机制。也为它添加一个 import 语句。

```
import android.os.AsyncTask; 
```

稍后，我们将尝试连接一个波段设备。我们将把这个实例存储在一个私有成员中，这样我们将来就可以通过各种方法来访问它。

```
private BandClient client = null; 
```

现在，让我们添加一个继承自`AsyncTask`的类。

```
private class BandConnectTask extends AsyncTask<Void, Void, Void> {
  @Override
  protected Void doInBackground(Void... params) {
    return null;
  }
} 
```

就像 iOS 部分一样，我们假设您对 Android 开发非常熟悉，创建一个带有按钮和标签的简单 UI 是轻而易举的事情。关于如何创建用户界面并将其与活动端的实例和动作挂钩的细节不在本文的讨论范围之内。现在我们需要添加一个按钮，并将它与一个侦听器挂钩。将其添加到您的应用程序中。

在监听器内部，我们将执行负责连接到乐队的后台任务:

```
btnConnect.setOnClickListener(new OnClickListener() {
  @Override
  public void onClick(View v) {
    new BandConnectTask().execute();
  } 
```

让我们实现连接逻辑。运行此应用程序的设备需要与至少一个波段配对(可以是多个)。该代码连接到它找到的第一个波段，但如果需要，您可以提供逻辑来允许用户为应用程序选择特定的波段。一旦我们得到实例，我们就连接到它。

```
protected Void doInBackground(Void... params) {
  BandInfo[] devices = BandClientManager.getInstance().getPairedBands();
  client = BandClientManager.getInstance().create(getBaseContext(), devices[0]);
  try {
    ConnectionState state = client.connect().await();
    if(state == ConnectionState.CONNECTED) {
        // do work on success
    } else {
        // do work on failure
    }
  } catch (InterruptedException e) {
    e.printStackTrace();
  } catch (BandException e) {
    e.printStackTrace();
  }
  return null;
} 
```

## Android——访问传感器

假设一切顺利，我们已经成功连接到一个波段，我们现在可以开始访问传感器的数据馈送。然而，就像 iOS 一样，一些传感器需要明确的用户同意。目前，只有心率和 RR 间期传感器需要同意，但这可能会在没有警告的情况下改变。当您需要访问传感器时，最好检查同意情况。

我们假设您的视图中还有另一个按钮，可以调用我们讨论的下一个方法。

首先，我们检查用户是否同意使用有问题的传感器。由于我们通过调用`requestHeartRateConsent`请求访问心率传感器，它将向用户显示一个对话框，要求用户同意。

![Consent on Android](img/6eddeeef8c85ef7a49730fe7cc0ca465.png)

Android 上的同意

```
btnStart.setOnClickListener(new View.OnClickListener() {
  @Override
  public void onClick(View v)  {
    if(client.getSensorManager().getCurrentHeartRateConsent() !=
            UserConsent.GRANTED) {
      client.getSensorManager().requestHeartRateConsent(MainActivity.this, new HeartRateConsentListener() {
        @Override
        public void userAccepted(boolean consentGiven) {
        }
      });
    }
  }
}); 
```

假设用户已经同意，我们现在可以开始接收传感器数据。如果用户已经通过`userAccepted`方法给予同意，或者如果当前状态是用户已经给予同意，则可以调用该方法。我们调用方法`registerHeartRateEventListener`,并传递给它一个我们将立即创建的特定侦听器。

```
private void startRecievingData() {
  try {
    client.getSensorManager().registerHeartRateEventListener(heartRateListener);
  } catch(BandException ex) {
  }
} 
```

在本教程中，我们在`MainActivity`上创建了这个监听器，如下所示:

```
final BandHeartRateEventListener heartRateListener = new
  BandHeartRateEventListener() {
    @Override
    public void onBandHeartRateChanged(final BandHeartRateEvent event) {
    }
  }; 
```

这应该足够编译了，但是我们想更进一步，更新 UI。我们再次假设你有一个`TextView`供你使用*(如果没有，请在你的布局中添加一个)*。因为我们正在使用 UI 元素，所以我们必须在 UI 线程上处理更新，所以我们将调用 activity `runOnUI`方法，并用从事件中获得的数据更新 UI。

```
final BandHeartRateEventListener heartRateListener = new
  BandHeartRateEventListener() {
    @Override
    public void onBandHeartRateChanged(final BandHeartRateEvent event) {
      MainActivity.this.runOnUiThread(new Runnable() {
        @Override
        public void run() {
          updateText.setText(String.format("Heart Rate = %d beats per minute\n"
            + "Quality = %s\n", event.getHeartRate(), event.getQuality()));
        }
      });
    }
  }; 
```

一旦我们完成对传感器的访问，最好取消对传感器的订阅。这一部分很简单——只需调用`unregisterHeartRateEventListener`就可以了。

```
btnStop.setOnClickListener(new View.OnClickListener() {
  @Override
  public void onClick(View v)  {
    try {
      client.getSensorManager().unregisterHeartRateEventListener(heartRateListener);
    } catch(BandIOException ex) {
    }
  }
}); 
```

通过几行简单的代码，我们现在已经从头开始创建了一个 Android 应用程序，将它连接到一个表带，并从它那里接收心率数据。

现在，让我们启动 Visual Studio，看看 Windows 如何处理相同的场景。

## windows–设置 Band SDK 环境

所有版本的 Microsoft Band SDK for Windows 平台都需要 Visual Studio 2013 或更高版本。

如果您想在 Windows 通用平台上使用 SDK，您还需要通过 Visual Studio 安装程序安装通用平台功能。

如果您希望将 SDK 用于 Windows Phone (8.1 及更高版本)，您还需要安装 Windows Phone SDK add-on for Visual Studio。

在本教程中，我们将使用 Windows 10 通用平台应用程序。相同的代码将能够在 Windows Mobile 和 Windows desktops 设备上运行。

创建 Windows 通用平台应用程序的步骤包括:

*   创建 Windows 空白应用程序(通用)。
*   在解决方案资源管理器中右键单击项目名称，并选择*“管理 Nuget 包…”*。
*   点击浏览，搜索“微软乐队”。
*   安装*“微软。波段"*打包成我们的项目。
*   双击 *Package.appxmanifest* ，在功能选项卡上勾选“蓝牙”和“邻近度”。对于早期版本的 Windows 平台，这可能会有所改变，请参考 Band 文档以了解更多详细信息。

现在，我们准备开始编码。

## Windows-连接到波段设备

导航到 *MainPage.xaml.cs* 文件。添加以下 using 语句，以便访问 Micorsoft Band SDK。

```
using Microsoft.Band;
using Microsoft.Band.Sensors; 
```

让我们添加一个私有字段来存储乐队的客户端实例。我们将能够通过代码中的各种方法来访问它。

```
private IBandClient _client; 
```

在本教程中，我们假设您了解如何向用户界面添加按钮和文本块，以及如何通过代码隐藏来访问它们。为了简单起见，我们将避免使用 MVVM 模式，但是下面的代码可以很容易地转换成绑定到 UI 的命令和属性。Band SDK 严重依赖于[异步等待模式](https://msdn.microsoft.com/en-us/library/hh191443.aspx)。确保用关键字`"async"`修饰正在调用和等待其他方法的方法。

使用`BandClientManager` singleton，我们可以访问与运行该应用的设备配对的所有 Band 设备。在本教程中，我们选择第一个，但在您的代码中，您可以选择想要使用的波段。当`ConnectAsync`方法完成时，我们应该与乐队建立连接。如果出错，我们将得到一个异常。

```
private async void ConnectClick(object sender, RoutedEventArgs e)
{
  var pairedBands = await BandClientManager.Instance.GetBandsAsync();
  try
  {
    _client = await
      BandClientManager.Instance.ConnectAsync(pairedBands[0]);
  }
  catch (BandException ex)
  {
    // handle a Band connection exception
  }
} 
```

## Windows —访问传感器

假设一切顺利，我们已经成功连接到一个波段，我们现在可以开始访问传感器的数据馈送。就像上面的 iOS 和 Android 一样，有些传感器需要明确的用户同意(心率和 RR 间期传感器)，最佳做法是在您需要访问传感器时检查同意，因为需要许可的传感器可能会随时更改。

我们再次假设您的视图中还有另一个按钮，可以调用我们接下来将讨论的方法。

首先，我们检查用户是否同意使用有问题的传感器。当我们要求使用`RequestUserConsentAsync`访问心率传感器时，它会向用户显示一个对话框，要求用户同意。

![Heart rate consent on Windows](img/33b96682e8f4e137435ee82412975b2a.png)

Windows 上的同意对话框

```
private async void StartClick(object sender, RoutedEventArgs e)
{
  if (_client.SensorManager.HeartRate.GetCurrentUserConsent() !=
      UserConsent.Granted)
  {
    // user hasn’t consented, request consent
    var result = await
        _client.SensorManager.HeartRate.RequestUserConsentAsync();
    if (result && _client.SensorManager.HeartRate.GetCurrentUserConsent() ==
      UserConsent.Granted)
    {
      StartRecievingData();
    }
  }
  if (_client.SensorManager.HeartRate.GetCurrentUserConsent() ==
    UserConsent.Granted)
  {
    StartRecievingData();
  }
} 
```

假设用户已经被批准访问，我们现在可以开始接收传感器数据。如果用户已经通过`RequestUserConsentAsync`的结果给予同意，或者如果当前状态是用户已经给予同意，则可以调用该方法。我们调用方法`StartReadingsAsync`并订阅一个事件，该事件将在传感器指定的时间间隔内触发:

```
private async void StartRecievingData()
{
  _client.SensorManager.HeartRate.ReadingChanged += async (sender, args) =>
  {
    // do work when the reading changes (i.e., update a UI element)
  };
  try
  {
    await _client.SensorManager.HeartRate.StartReadingsAsync();
  }
  catch (BandException ex)
  {
    // handle a Band connection exception
  }
} 
```

现在，我们想更新用户界面。然而，我们需要担心另一个问题——为了更新 UI，这段代码需要在 UI 线程上运行。实现这一点的机制是调用`Dispatcher.RunAsync`并向其传递一个 lambda 表达式，该表达式反过来确保在 UI 线程上运行。我们加入了 C# 6.0 的一个新特性，[字符串插值](https://msdn.microsoft.com/en-us/library/dn961160.aspx)，它以一种比使用`String.Format`方法更清晰的方式格式化字符串。

```
private async void StartRecievingData()
{
  _client.SensorManager.HeartRate.ReadingChanged += async (sender, args) =>
  {
    await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
      TextBlock.Text =
        $"Heart Rate = {args.SensorReading.HeartRate} Quality = {args.SensorReading.Quality}");
  };
  try
  {
    await _client.SensorManager.HeartRate.StartReadingsAsync();
  }
  catch (BandException ex)
  {
    // handle a Band connection exception
  }
} 
```

一旦我们完成对传感器的访问，最好取消对传感器的订阅。对`StopReadingsAsync`的一个简单调用应该可以完成这个任务。

```
private async void StopClick(object sender, RoutedEventArgs e)
{
  await _client.SensorManager.HeartRate.StopReadingsAsync();
} 
```

我们的 Windows 应用程序现在可以连接到腕带，并从中接收心率数据。

## 包扎

我们已经介绍了如何开始、连接和接收来自 Microsoft Band 设备的真实数据。Microsoft Band 配备了传感器，可以为您的连接应用程序提供全方位的数据。

这还不是你可以用这个带做的所有事情——从这里你可以添加你自己的磁贴，并通过按钮和一个独特的用户界面将功能连接到这些磁贴上。您甚至可以创建乐队皮肤/主题，以进一步个性化乐队。SDK 是完全跨平台的，支持当今的主要平台——iOS、Android 和 Windows。

为乐队构建应用程序的时机已经成熟，我们迫不及待地想看看你能用它实现什么！

感谢您的时间，我希望你喜欢这个教程，并发现它是有用的。如果你有想法或问题，请在下面添加。

## 分享这篇文章