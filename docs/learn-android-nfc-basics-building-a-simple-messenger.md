# 通过构建一个简单的 Messenger 学习 Android NFC 基础知识

> 原文：<https://www.sitepoint.com/learn-android-nfc-basics-building-a-simple-messenger/>

*这篇文章由[蒂姆·塞韦里安](https://github.com/timseverien)进行了同行评审。感谢所有 SitePoint 的[同行评审](https://www.sitepoint.com/introduction-to-sitepoints-peer-review)让 SitePoint 的内容尽可能做到最好！*

* * *

NFC(近场通信)是设备之间或设备与 NFC“标签”之间的短程无线通信方法。NFC 一般不局限于 Android 或移动设备，但本教程专门针对 NFC 的 Android 实现。

本教程结束时，你将了解 NFC 的基本概念以及如何在 Android 设备之间建立基本通信。要完成本教程，您需要 API 14 或更高版本。虽然使用了 API 16 中引入的一些函数，但它们是方便的函数，不是必需的。

你可以在 [GitHub](https://github.com/sitepoint-editors/NFC-Simple-Messenger) 上找到本教程的完整代码。

## NFC 格式

NFC 有一个由 NFC 论坛创建的通用标准，以确保接口可以跨不同系统工作。这种格式是“NDEF”(NFC 数据交换格式)，让我们知道标签中的信息可能如何呈现给我们，并提供一种方法来确保我们创建的数据可以对尽可能多的用户有用。

现在，这就是我要说的关于格式化的全部内容，但是我们将会回来讨论它。

## 标签调度系统

Android 操作系统通过其“NFC 标签调度系统”处理 NFC。这是系统的一部分，独立于您的应用程序，您几乎无法控制。它会不断寻找(假设设备上未禁用 NFC)可以与之接口的 NFC 设备。如果设备距离另一个支持 NFC 的设备或 NFC 标签不到 4 厘米，系统将发送一个意向，这就是我们接收数据的方式。

打开 Android Studio，创建一个包含空白活动的项目，然后我们就可以开始了。

## 过滤 NFC 意图

在过滤 NFC 意图时，您希望尽可能具体。这是为了避免选择器对话框出现在可以处理意图的应用程序中。通常情况下，选择器是没有问题的，而且让它(或强迫它)显示出来通常是首选行为，但 NFC 不是这样。

NFC 要求设备之间的距离在几厘米以内。如果我们允许选择器显示，我们的用户很可能会把设备移回自己身边来查看并取消交互。

标签分派系统有三个动作，它可以附加到它创建的意图，以响应发现它可以读取、写入或通信的内容。调度系统将发出几个意向，如果一个意向找不到一个活动来处理它，则发送列表中的下一个意向。

标签分派系统将附加到其意图的动作是:

1.  `ACTION_NDEF_DISCOVERED`–如果找到的信息格式为 NDEF，则发送此消息。
2.  `ACTION_TECH_DISCOVERED`–如果第一个失败，或者数据以不熟悉的方式格式化，则发送此消息
3.  最后也是最普通的。请记住，我们希望在此之前捕获意图，因为我们可能会有多个活动指定了这种一般的东西。

我们将创建一个简单的 messenger 来发送和接收字符串列表。

打开 *AndroidManifest.xml* 并将以下意图过滤器添加到主活动中:

```
<intent-filter>
       <action android:name="android.nfc.action.NDEF_DISCOVERED" />
       <category android:name="android.intent.category.DEFAULT"/>
       <data android:mimeType="text/plain" />
</intent-filter>
```

与任何 Android 项目一样，我们将不得不请求适当的许可。将以下权限和功能添加到 *AndroidManifest.xml* :

```
<uses-permission android:name="android.permission.NFC" />
<uses-feature android:name="android.hardware.nfc" android:required="true"/>
```

为了让后面的事情更顺利，将主活动的启动模式设置为“单个任务”。这将允许我们处理发送到我们的活动的意图，而不必重新创建活动，给我们的用户更流畅的感觉。

```
<activity
           android:launchMode="singleTask"
           android:name=".MainActivity"
           android:label="@string/app_name" >
```

## 简单界面

我们需要一种方法来添加要发送给字符串数组的消息。你可以创建你自己的方法，或者使用我下面的简单接口。

将 *activity_main.xml* 更改为:

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">

   <EditText
       android:id="@+id/txtBoxAddMessage"
       android:layout_width="match_parent"
       android:layout_height="wrap_content" />

   <Button
       android:id="@+id/buttonAddMessage"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:onClick="addMessage"
       android:layout_below="@+id/txtBoxAddMessage"
       android:layout_centerHorizontal="true" />

   <TextView
       android:id="@+id/txtMessagesReceived"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:layout_below="@+id/buttonAddMessage"
       android:layout_alignParentEnd="true"
       android:layout_alignParentRight="true"/>

   <TextView
       android:id="@+id/txtMessageToSend"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:layout_alignTop="@+id/txtMessagesReceived"
       android:layout_alignParentLeft="true"
       android:layout_alignParentStart="true"/>

</RelativeLayout>
```

将*MainActivity.java*更新如下:

```
public class MainActivity extends AppCompatActivity {
    //The array lists to hold our messages
    private ArrayList<String> messagesToSendArray = new ArrayList<>();
    private ArrayList<String> messagesReceivedArray = new ArrayList<>();

    //Text boxes to add and display our messages
    private EditText txtBoxAddMessage;
    private TextView txtReceivedMessages;
    private TextView txtMessagesToSend;

    public void addMessage(View view) {
        String newMessage = txtBoxAddMessage.getText().toString();
        messagesToSendArray.add(newMessage);

        txtBoxAddMessage.setText(null);
        updateTextViews();

        Toast.makeText(this, "Added Message", Toast.LENGTH_LONG).show();
    }

    private  void updateTextViews() {
        txtMessagesToSend.setText("Messages To Send:\n");
        //Populate Our list of messages we want to send
        if(messagesToSendArray.size() > 0) {
            for (int i = 0; i < messagesToSendArray.size(); i++) {
                txtMessagesToSend.append(messagesToSendArray.get(i));
                txtMessagesToSend.append("\n");
            }
        }

        txtReceivedMessages.setText("Messages Received:\n");
        //Populate our list of messages we have received
        if (messagesReceivedArray.size() > 0) {
            for (int i = 0; i < messagesReceivedArray.size(); i++) {
                txtReceivedMessages.append(messagesReceivedArray.get(i));
                txtReceivedMessages.append("\n");
            }
        }
    }

    //Save our Array Lists of Messages for if the user navigates away
    @Override
    public void onSaveInstanceState(@NonNull Bundle savedInstanceState) {
        super.onSaveInstanceState(savedInstanceState);
        savedInstanceState.putStringArrayList("messagesToSend", messagesToSendArray);
        savedInstanceState.putStringArrayList("lastMessagesReceived",messagesReceivedArray);
    }

    //Load our Array Lists of Messages for when the user navigates back
    @Override
    public void onRestoreInstanceState(@NonNull Bundle savedInstanceState) {
        super.onRestoreInstanceState(savedInstanceState);
        messagesToSendArray = savedInstanceState.getStringArrayList("messagesToSend");
        messagesReceivedArray = savedInstanceState.getStringArrayList("lastMessagesReceived");
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        txtBoxAddMessage = (EditText) findViewById(R.id.txtBoxAddMessage);
        txtMessagesToSend = (TextView) findViewById(R.id.txtMessageToSend);
        txtReceivedMessages = (TextView) findViewById(R.id.txtMessagesReceived);
        Button btnAddMessage = (Button) findViewById(R.id.buttonAddMessage);

        btnAddMessage.setText("Add Message");
        updateTextViews();
    }
}
```

## 检查 NFC 支持

在*MainActivity.java*`onCreate()`方法中添加以下内容，以便在设备不支持 NFC 时进行处理:

```
//Check if NFC is available on device
mNfcAdapter = NfcAdapter.getDefaultAdapter(this);
if(mNfcAdapter != null) {
    //Handle some NFC initialization here
  }
  else {
      Toast.makeText(this, "NFC not available on this device",
                Toast.LENGTH_SHORT).show();
  }
```

确保在类定义的顶部创建`mNfcAdapter`变量:

```
private NfcAdapter mNfcAdapter;
```

## 创造我们的信息

Android 提供了有用的类和函数，允许我们打包数据。为了符合 NDEF，我们可以创建包含一个或多个`NdefRecord`的`NdefMessage`

要发送消息，我们必须首先创建它。有两种主要的方法来处理这个问题:

1.  调用`NfcAdapter`类中的`setNdefPushMessage()`。这将接受在检测到另一个支持 NFC 的设备时发送的`NdefMessage`。
2.  覆盖回调，这样我们的`NdefMessage`将只在需要发送的时候被创建。

如果数据不会改变，第一种方法是首选方法。我们的数据会发生变化，所以我们将使用第二个选项。

为了处理消息发送，我们需要指定回调来响应 NFC 事件。因为我们将更新我们的数据，所以我们希望只在需要发送时才创建消息。

像这样更新活动:

```
public class MainActivity extends Activity
       implements NfcAdapter.OnNdefPushCompleteCallback,
                  NfcAdapter.CreateNdefMessageCallback
```

覆盖相关功能:

```
@Override
public void onNdefPushComplete(NfcEvent event) {
    //This is called when the system detects that our NdefMessage was
    //Successfully sent.
    messagesToSendArray.clear();
}

@Override
public NdefMessage createNdefMessage(NfcEvent event) {
    //This will be called when another NFC capable device is detected.
    if (messagesToSendArray.size() == 0) {
        return null;
    }
    //We'll write the createRecords() method in just a moment
    NdefRecord[] recordsToAttach = createRecords();
    //When creating an NdefMessage we need to provide an NdefRecord[]
    return new NdefMessage(recordsToAttach);
}
```

现在确保在`onCreate`方法中指定这些回调:

```
//Check if NFC is available on device
mNfcAdapter = NfcAdapter.getDefaultAdapter(this);
if(mNfcAdapter != null) {
    //This will refer back to createNdefMessage for what it will send
    mNfcAdapter.setNdefPushMessageCallback(this, this);

    //This will be called if the message is sent successfully
    mNfcAdapter.setOnNdefPushCompleteCallback(this, this);
}
```

## 创建记录

在`NdefRecord`类中有多个实用函数可以返回一个正确格式化的`NdefRecord`，但是为了理解这个概念并给我们更多的灵活性，我们将首先手动创建一个`NdefRecord`。

在 NDEF，一张唱片有四个部分:

1.  一个从常量列表中指定有效负载类型名称的`short`。
2.  一个可变长度的`byte[]`给出了我们类型的更多细节。
3.  用作唯一标识符的可变长度`byte[]`。这既不需要也不经常使用。
4.  可变长度`byte[]`是我们的实际有效载荷

将此功能添加到*MainActivity.java*:

```
public NdefRecord[] createRecords() {

    NdefRecord[] records = new NdefRecord[messagesToSendArray.size()];

    for (int i = 0; i < messagesToSendArray.size(); i++){

        byte[] payload = messagesToSendArray.get(i).
           getBytes(Charset.forName("UTF-8"));

        NdefRecord record = new NdefRecord(
                NdefRecord.TNF_WELL_KNOWN,  //Our 3-bit Type name format
                NdefRecord.RTD_TEXT,        //Description of our payload
                new byte[0],                //The optional id for our Record
                payload);                   //Our payload for the Record

        records[i] = record;
    }
    return records;
}
```

因为我们既写发送者又写接收者，所以我们可以具体说明我们希望如何处理我们的数据。我们可以调用`NdefRecord.createApplicationRecord`来附加一个特殊格式的`NdefRecord`来告诉操作系统哪个应用程序应该处理数据。系统将尝试先打开应用程序来处理数据，然后再打开其他应用程序。

我们在`NdefRecord[]`数组中的什么地方包含这个记录并不重要，只要它出现在任何地方都可以工作。确保将我们的`NdefRecord[]`的长度调整为 1，以容纳额外的记录，并在`createRecords()`函数返回之前添加以下内容。

```
//Remember to change the size of your array when you instantiate it.
records[messagesToSendArray.size()] =
           NdefRecord.createApplicationRecord(getPackage());
```

创建和附加 Android 应用程序记录的一个优点是，如果 Android 找不到该应用程序，它将打开与 Google Play 商店的连接，并尝试下载您的应用程序(假设它存在)。

**注意**:这并不能保证交易安全，也不能保证你的应用程序会打开交易。仅包含应用程序记录进一步指定了我们对操作系统的偏好。如果另一个当前在前台的活动调用[nfcadapter . enableforegrounddispatch](https://developer.android.com/reference/android/nfc/NfcAdapter.html#enableForegroundDispatch%28android.app.Activity,%20android.app.PendingIntent,%20android.content.IntentFilter%5B%5D,%20java.lang.String%5B%5D%5B%5D%29)它可以在意图到达我们之前捕捉到它，除了让我们的活动在前台之外，没有其他方法可以阻止它。尽管如此，这已经是我们能够确保我们的应用程序能够处理这些数据的最接近的了。

如前所述，通常最好使用提供的实用函数来创建记录。这些函数大部分都是在 API 16 中引入的，我们是为 14 或更高版本编写的。因此，我们涵盖了所有基础，让我们包括一个 API 级别的检查，并以首选方式创建我们的记录，如果该功能对我们可用的话。将`createRecords()`功能改为:

```
public NdefRecord[] createRecords() {
    NdefRecord[] records = new NdefRecord[messagesToSendArray.size() + 1];
        //To Create Messages Manually if API is less than
        if (Build.VERSION.SDK_INT < Build.VERSION_CODES.JELLY_BEAN) {
            for (int i = 0; i < messagesToSendArray.size(); i++){
               byte[] payload = messagesToSendArray.get(i).
                             getBytes(Charset.forName("UTF-8"));
               NdefRecord record = new NdefRecord(
                       NdefRecord.TNF_WELL_KNOWN,      //Our 3-bit Type name format
                       NdefRecord.RTD_TEXT,            //Description of our payload
                       new byte[0],                    //The optional id for our Record
                       payload);                       //Our payload for the Record

               records[i] = record;
           }
       }
       //Api is high enough that we can use createMime, which is preferred.
       else {
           for (int i = 0; i < messagesToSendArray.size(); i++){
               byte[] payload = messagesToSendArray.get(i).
                                getBytes(Charset.forName("UTF-8"));

               NdefRecord record = NdefRecord.createMime("text/plain",payload);
               records[i] = record;
           }
       }
      records[messagesToSendArray.size()] =
       NdefRecord.createApplicationRecord(getPackageName());
       return records;
  }
```

## 处理消息

接收到的意向将包含一个`NdefMessage[]`数组。既然知道长度，那就好加工了。

```
private void handleNfcIntent(Intent NfcIntent) {
    if (NfcAdapter.ACTION_NDEF_DISCOVERED.equals(NfcIntent.getAction())) {
            Parcelable[] receivedArray =
                NfcIntent.getParcelableArrayExtra(NfcAdapter.EXTRA_NDEF_MESSAGES);

        if(receivedArray != null) {
            messagesReceivedArray.clear();
            NdefMessage receivedMessage = (NdefMessage) receivedArray[0];
            NdefRecord[] attachedRecords = receivedMessage.getRecords();

            for (NdefRecord record:attachedRecords) {
                String string = new String(record.getPayload());
                //Make sure we don't pass along our AAR (Android Application Record)
                if (string.equals(getPackageName())) { continue; }
                messagesReceivedArray.add(string);
            }
            Toast.makeText(this, "Received " + messagesReceivedArray.size() +
                    " Messages", Toast.LENGTH_LONG).show();
            updateTextViews();
        }
        else {
            Toast.makeText(this, "Received Blank Parcel", Toast.LENGTH_LONG).show();
        }
    }
}

@Override
public void onNewIntent(Intent intent) {
        handleNfcIntent(intent);
}
```

我们覆盖了`onNewIntent`,这样我们就可以接收和处理消息，而不需要创建新的活动。这不是必须的，但会让一切都变得流畅。在`onCreate()`和`onResume()`函数中添加一个对`handleNfcIntent`的调用，以确保所有情况都得到处理。

```
@Override
  public void onResume() {
      super.onResume();
      updateTextViews();
      handleNfcIntent(getIntent());
  }
```

就是这样！你应该有一个简单的功能 NFC 信使。附加不同类型的文件就像指定不同的 mime 类型并附加您想要发送的文件的二进制文件一样简单。要获得受支持类型及其便利构造函数的完整列表，请查看 Android 文档中的 [NdefMessage](https://developer.android.com/reference/android/nfc/NdefMessage.html) 和 [NdefRecord](https://developer.android.com/reference/android/nfc/NdefRecord.html) 类。Android 上有更多复杂的功能，例如模拟 NFC 标签，以便我们可以被动阅读，但这超出了简单的 messenger 应用程序。

## 分享这篇文章