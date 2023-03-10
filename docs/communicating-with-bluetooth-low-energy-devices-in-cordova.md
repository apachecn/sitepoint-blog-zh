# 与科尔多瓦的蓝牙低能耗设备通信

> 原文：<https://www.sitepoint.com/communicating-with-bluetooth-low-energy-devices-in-cordova/>

在本教程中，您将使用 Cordova 和蓝牙低能耗(BLE)外设构建一个考勤应用程序。您将使用 Node.js 创建自己的 BLE 外设，并使用 Cordova 应用程序与之通信。

我假设你已经知道科尔多瓦和离子框架。如果没有，看看这篇关于使用高级应用框架 Ionic 构建简单应用的教程。

你可以在 [Github](https://github.com/sitepoint-editors/ionic-ble-attendance) 上找到最终的项目。

## 蓝牙低能耗概念

首先，我想弄清楚一些概念。使用 ble 可以连接两种可能的设备:

*   **中枢**:也叫‘主’。这是启动与从机或外设连接的设备。这通常是智能手机、平板电脑或电脑。在本教程中，中心设备是运行您将创建的应用程序的智能手机。
*   **外设**:也叫‘从’。它负责广告并等待中央设备连接到它。这些设备的例子包括健身追踪器(例如 fitbit)或信标。在本教程中，我要使用的外设是一个树莓派 3。如果你没有，它也可以是你的智能手机或电脑。只要您使用的蓝牙设备支持蓝牙 4.0，它就应该可以工作。

数据交换是通过通用属性配置文件(GATT)实现的。GATT 定义了两台设备传输数据的方式。它由**服务**和**特征**组成。**服务**是封装了设备行为的特征集合。例如，有一个[血压服务](https://www.bluetooth.com/specifications/gatt/viewer?attributeXmlFile=org.bluetooth.service.blood_pressure.xml)，它公开来自血压监视器的血压数据。**特征**是服务中可用的不同类型的数据。对于血压服务，有一个血压测量特征、中间袖带压力和血压特征。稍后，您将创建自己的服务及其特征。

## 构建应用程序

这个考勤应用程序有两个组件，BLE 外设和与之对话的应用程序。BLE 外围设备宣传一种允许连接的设备向其写入数据的服务。应用程序通过其特性将数据写入该服务。具体来说，你会询问与会者的名字和姓氏。用户将点击**扫描**按钮，这将列出用户设备附近的所有 BLE 外围设备。然后，用户连接到这些外围设备之一。一旦连接，应用程序将要求用户输入他们的名字和姓氏。最后，用户点击**出席**按钮，向 BLE 外围设备提交他们的名字。

下面是几个显示最终输出的截图:

![scan devices](img/0e3f005ab01fb6338248a041a3c3f035.png)

![attendance form](img/2d1c7ac9e7b0bcfc6afae877b0870365.png)

现在是你构建应用程序的时候了。首先创建一个新的 Ionic 应用程序:

```
ionic start ionicBLEAttendance blank 
```

为 Cordova 添加[蓝牙低能耗中央插件](https://github.com/don/cordova-plugin-ble-central)。这是一个插件，您将使用它与 BLE 外围设备进行对话，您将在以后构建该插件。

```
cordova plugin add cordova-plugin-ble-central 
```

在 *www* 目录中导航，这将是您将要工作的基本目录。打开 index.html 的*并用以下内容替换现有内容:*

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no, width=device-width">
    <title></title>

    <link href="lib/ionic/css/ionic.css" rel="stylesheet">
    <link href="css/style.css" rel="stylesheet">

    <script src="lib/ionic/js/ionic.bundle.js"></script>

    <script src="cordova.js"></script>

    <script src="js/app.js"></script>
    <script src="js/factories/DeviceFactory.js"></script>
    <script src="js/controllers/HomeController.js"></script>
    <script src="js/controllers/DeviceController.js"></script>
  </head>
  <body ng-app="starter">
    <ion-nav-view></ion-nav-view>
  </body>
</html> 
```

在这段代码中，除了默认文件之外，您还链接了三个新文件。创建这些文件。

打开 *app.js* ，在`$ionicPlatform.ready`事件的回调函数中添加以下内容。这将检查设备中是否启用了蓝牙。如果它没有启用，它会通过提示用户来启用它。

```
ble.isEnabled(
  function(){
    // Bluetooth is enabled
  },
  function(){
    // Bluetooth not yet enabled so we try to enable it
    ble.enable(
      function(){
        // bluetooth now enabled
      },
      function(err){
        alert('Cannot enable bluetooth');
      }
    );
  }
); 
```

在`run`功能后，添加路线配置:

```
.config(function($stateProvider, $urlRouterProvider) {
  $stateProvider

  .state('home', {
    url: '/home',
    templateUrl: 'templates/home.html'
  })

  .state('device', {
    url: '/device/:id',
    templateUrl: 'templates/device.html'
  });

  $urlRouterProvider.otherwise('/home');
}); 
```

### 设备工厂

设备工厂充当外围设备扫描返回的设备的数据存储。它有添加设备、获取所有设备、获取特定设备和重置数据存储的方法。打开*js/factories/device factory . js*，添加以下代码。

```
(function(){
  angular.module('starter')
  .factory('DeviceFactory', [DeviceFactory]);

  function DeviceFactory(){
    var devices = [];
    return {
      addDevice: function(device){
        devices.push(device);
      },

      getDevices: function(){
        return devices;
      },

      getDevice: function(id){
        var device_found = devices.filter(function(device){
          return device.id == id;
        });
        return device_found[0];
      },

      reset: function(){
        devices = [];
      }

    };
  }

})(); 
```

### 主页

主页是用户打开应用程序时看到的默认页面。此页面允许用户扫描附近的 BLE 外围设备并连接到它们。

创建*模板/home.html* 并添加以下内容:

```
<ion-view title="Ionic BLE Attendance" ng-controller="HomeController as home_ctrl">
  <header class="bar bar-header bar-stable">
    <h1 class="title">Ionic Bluetooth Attendance</h1>
  </header>

  <ion-content class="has-header padding">
    <button class="button button-positive button-full" ng-click="scan()">
      Scan
    </button>

    <div class="card" ng-if="devices.length">
      <div class="item item-divider">
        Devices Found
      </div>
      <div class="item">
        <ul class="list">
          <li class="item item-button-right" ng-repeat="device in devices">
            {{device.name}}
            <button class="button button-balanced" ng-click="connect(device.id)">
            connect
            </button>
          </li>
        </ul>
      </div>
    </div>

  </ion-content>

</ion-view> 
```

主页后面的代码是家庭控制器。打开*js/controllers/home controller . js*，添加以下代码:

```
(function(){
  angular.module('starter')
  .controller('HomeController', ['$scope', '$state', 'DeviceFactory', HomeController]);

  function HomeController($scope, $state, DeviceFactory){

    $scope.devices = []; // the devices listed in the page

    $scope.scan = function(){

      DeviceFactory.reset();
      ble.startScan(
        [],
        function(device){
          if(device.name){
            DeviceFactory.addDevice({ 'id': device.id, 'name': device.name });
          }
        },
        function(err){
          alert('Scanning failed. Please try again.');
        }
      );

      setTimeout(
          ble.stopScan,
          1500,
          function(){
            $scope.$apply(function(){
              $scope.devices = DeviceFactory.getDevices();
            });
          },
          function(){
            // Stopping scan failed
          }
      );

    }

    $scope.connect = function(device_id){
      ble.connect(
        device_id,
        function(res){
          $state.go('device', { 'id': device_id });
        },
        function(err){
          alert('Something went wrong while trying to connect. Please try again');
        }
      );
    }

  }

})(); 
```

在上面的代码中，`scan`方法首先清空设备工厂中存储的外设数组。然后它会扫描附近的外围设备。`startScan`方法接受一组要发现的服务作为其第一个参数。在这种情况下，您将传递一个空数组，该数组将发现附近外围设备广告的任何服务。第二个参数是每次发现新设备时执行的回调函数，使用其`addDevice`方法将发现的设备添加到设备工厂。`addDevice`方法接受一个包含设备 ID 和名称的对象。设备 ID 可以是 UUID 或 MAC 地址。

```
$scope.scan = function(){
    DeviceFactory.reset();
    ble.startScan(
      [],
      function(device){
        if(device.name){
          DeviceFactory.addDevice({ 'id': device.id, 'name': device.name });
        }
      },
      function(err){
        alert('Scanning failed. Please try again.');
      }
    );
} 
```

`ble.startScan`方法无限执行，这就是为什么你必须调用`ble.stopScan`方法来停止扫描。你在 1.5 秒(1500 毫秒)后这样做，然后`$scope`用存储在设备工厂中的所有设备更新，反过来更新 UI 以列出发现的所有设备。

```
setTimeout(
    ble.stopScan,
    1500,
    function(){
      $scope.$apply(function(){
        $scope.devices = DeviceFactory.getDevices();
      });
    },
    function(){
      // Stopping scan failed
    }
); 
```

以下是您从一个`ble.startScan`呼叫中得到的示例响应:

```
[  
   {  
      "advertising":{  

      },
      "id":"B8:XX:XX:XX:XX:XX",
      "rssi":-57,
      "name":"AttendanceApp"
   },
   {  
      "advertising":{  

      },
      "id":"E7:YY:YY:YY:YY:YY",
      "rssi":-67,
      "name":"Flex"
   }
] 
```

属性是一个 T1，这就是为什么它是空的。如果您想访问广告数据，可以将它转换为字符串。`rssi`是[接收信号强度指示](https://en.wikipedia.org/wiki/Received_signal_strength_indication)，它是信号强度的指示。

接下来是当用户点击发现的外设的*连接*按钮时调用的`connect`方法。传递给该方法的设备 ID 连接到外设。一旦应用程序连接到外设，它就导航到设备页面，将设备 ID 作为状态参数传入。

```
$scope.connect = function(device_id){
  ble.connect(
    device_id,
    function(res){
      $state.go('device', { 'id': device_id });
    },
    function(err){
      alert('Something went wrong while trying to connect. Please try again');
    }
  );
} 
```

以下是您调用`ble.connect`时的示例响应:

```
{  
   "characteristics":[  
      {  
         "characteristic":"2a00",
         "service":"1800",
         "properties":[  
            "Read"
         ]
      },
      {  
         "characteristic":"2a01",
         "service":"1800",
         "properties":[  
            "Read"
         ]
      },
      {  
         "descriptors":[  
            {  
               "uuid":"2902"
            }
         ],
         "characteristic":"2a05",
         "service":"1801",
         "properties":[  
            "Indicate"
         ]
      },
      {  
         "descriptors":[  
            {  
               "uuid":"2902"
            }
         ],
         "characteristic":"34cd",
         "service":"12ab",
         "properties":[  
            "Write"
         ]
      }
   ],
   "advertising":{  

   },
   "id":"B8:XX:XX:XX:XX:XX",
   "services":[  
      "1800",
      "1801",
      "12ab"
   ],
   "rssi":-55,
   "name":"AttendanceApp"
} 
```

它有一个数组`characteristics`，但是您感兴趣的是第四项，这是您稍后将创建的特征:

```
{  
   "descriptors":[  
      {  
         "uuid":"2902"
      }
   ],
   "characteristic":"34cd",
   "service":"12ab",
   "properties":[  
      "Write"
   ]
} 
```

### 设备页面

设备页面是用户连接到外围设备时导航到的页面。该页面允许用户输入他们的名字和姓氏，并点击*出席*按钮来记录他们的出席情况。

创建*模板/device.html* 并添加以下内容:

```
<ion-view title="Ionic BLE Attendance" ng-controller="DeviceController as device_ctrl" ng-init="init()">
  <header class="bar bar-header bar-stable">
    <button class="button icon ion-chevron-left" ng-click="backToHome()"></button>
    <h1 class="title">Ionic Bluetooth Attendance</h1>
  </header>
  <header class="bar bar-subheader">
    <h2 class="title">{{device.name}}</h2>
  </header>

  <ion-content class="has-header has-subheader padding">

    <div class="list">
      <label class="item item-input">
        <input type="text" ng-model="device_ctrl.attendee.firstname" placeholder="First Name">
      </label>
      <label class="item item-input">
        <input type="text" ng-model="device_ctrl.attendee.lastname" placeholder="Last Name">
      </label>
      <button class="item button button-positive button-full" ng-click="attend()">
      Attend
      </button>
    </div>

  </ion-content>

</ion-view> 
```

设备页面后面的代码是设备控制器。打开*js/controllers/device controller . js*并添加以下代码:

```
(function(){
  angular.module('starter')
  .controller('DeviceController', ['$scope', '$state', '$stateParams', 'DeviceFactory', DeviceController]);

  function DeviceController($scope, $state, $stateParams, DeviceFactory){

    var me = this;

    var service_id = '12ab';
    var characteristic_id = '34cd';

    me.attendee = {
      firstname: '',
      lastname: ''
    }

    $scope.init = function(){
      $scope.device = DeviceFactory.getDevice($stateParams.id);
    }

    $scope.attend = function(){
      ble.write(
        $stateParams.id,
        service_id,
        characteristic_id,
        btoa(JSON.stringify(me.attendee)),
        function(response){
          if(response == 'OK'){
            alert("Your attendance is recorded!");
            ble.disconnect($stateParams.id);
            $state.go('home');
          }
        },
        function(err){
          alert("Error occured while trying to record your attendance. Please try again.");
        }
      );
    }

    $scope.backToHome = function(){
      $state.go('home');
      ble.disconnect($stateParams.id);
    }

  }

})(); 
```

控制器内部是服务和特征 ID。当您连接到外围设备时，这些值作为设备对象中的一个属性返回。但你是在假设应用程序事先已经知道这些值，因为应用程序的开发者和 BLE 外设是同一个人。这就是为什么它们在这里被硬编码，而不是从`ble.connect`方法的返回值中提取。

```
var service_id = '12ab';
var characteristic_id = '34cd'; 
```

当设备页面初始化时，它调用`init`函数。通过调用设备工厂中的`getDevice`方法，初始化`device`对象的值。此方法返回给定设备 ID 的特定设备。

```
$scope.init = function(){
  $scope.device = DeviceFactory.getDevice($stateParams.id);
} 
```

当用户点击*出席*按钮时，该方法执行。它调用负责将数据写入特征的`ble.write`方法。这接受以下参数:

*   **设备 ID** :设备的 UUID 或 MAC 地址。
*   **服务 UUID** :分配给服务的唯一 ID。
*   **特性 UUID** :分配给特性的唯一 ID。
*   **数据**:你要发送的数据。在这种情况下，对象包含与会者的名字和姓氏。使用`JSON.stringify`将其转换为字符串，使用`btoa`将结果字符串转换为 base64 编码的字符串。这是因为你不能通过 BLE 发送普通字符串。

第四个和第五个参数是成功回调和错误回调函数。如果写入请求成功，使用`ble.disconnect`方法断开设备连接。传递给这个方法的`$stateParams.id`是您之前作为状态参数从家庭控制器传递的设备 ID。调用`disconnect`方法是因为一个外设一次只能连接到一个中央设备。这意味着当特定的中央设备连接时，它停止广告。当它停止广告时，它不会在扫描时显示。

```
$scope.attend = function(){
  ble.write(
    $stateParams.id,
    service_id,
    characteristic_id,
    btoa(JSON.stringify(me.attendee)),
    function(response){
      if(response == 'OK'){
        alert("Your attendance is recorded!");
        ble.disconnect($stateParams.id);
        $state.go('home'); // go back to home page
      }
    },
    function(err){
      alert("Error occurred while trying to record your attendance. Please try again.");
    }
  );
} 
```

还提供了手动返回主页的选项。这里调用了`ble.disconnect`方法。

```
$scope.backToHome = function(){
  $state.go('home');
  ble.disconnect($stateParams.id);
} 
```

## BLE 外围

现在是时候为 BLE 外围设备添加代码了。在继续之前，Intsall [Bleno](https://github.com/sandeepmistry/bleno) 因为您将使用这个 Node.js 模块来实现一个 BLE 外设。

现在您已经返回，为仿真外设创建一个新文件夹，创建一个 *package.json* 文件，并添加以下内容:

```
{
  "name": "ble-server",
  "version": "1.0.0",
  "description": "",
  "main": "attendance.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "bleno": "^0.4.0"
  }
} 
```

打开一个新的终端窗口，通过执行`npm install`安装依赖项。这就安装了 [bleno](https://github.com/sandeepmistry/bleno) 。

接下来，创建一个 *attendance.js* 文件，并添加以下代码:

```
var bleno = require('bleno');

var attendees = [];
var settings = {
  service_id: '12ab',
  characteristic_id: '34cd'
};

bleno.on('stateChange', function(state){
  if(state === 'poweredOn'){
    bleno.startAdvertising('AttendanceApp', ['12ab']);
  }else{
    bleno.stopAdvertising();
  }
});

bleno.on('advertisingStart', function(error){
    if(error){
      // error on advertise start
    }else{
      console.log('started..');
      bleno.setServices([
        new bleno.PrimaryService({
          uuid : settings.service_id,
          characteristics : [
            new bleno.Characteristic({
              value : null,
              uuid : settings.characteristic_id,
              properties : ['read', 'write'],
              onWriteRequest : function(data, offset, withoutResponse, callback){
                var attendee = JSON.parse(data.toString());
                attendee.time_entered = Date.now();
                attendees.push(attendee);
                console.log(attendees);
                callback(this.RESULT_SUCCESS);
              }
            })
          ]
        })
      ]);
    }
}); 
```

分解上面的代码，首先包括 bleno 模块:

```
var bleno = require('bleno'); 
```

接下来是`attendees`数组，它用作与会者列表的存储。其下是包含服务 ID 和特征 ID 的对象，用作以后服务和特征的 UUID。这些在前面的设备控制器中是相同的。

```
var attendees = []; // storage for the attendees
var settings = {
  service_id: '12ab',
  characteristic_id: '34cd'
}; 
```

监听设备状态何时改变。你想在设备开机后就开始广告，如果有其他情况就停止。如果您想根据设备状态执行任何其他操作，请查看[事件列表](https://github.com/sandeepmistry/bleno#events)。`startAdvertising`方法接受两个参数——您要给设备起的名字和要公布的一组服务 UUID。

```
bleno.on('stateChange', function(state){
  if(state === 'poweredOn'){
    bleno.startAdvertising('AttendanceApp', [settings.service_id]);
  }else{
    bleno.stopAdvertising();
  }
}); 
```

收听广告开始的时间:

```
bleno.on('advertisingStart', function(error){
    if(error){
      // error on advertise start
    }else{
        ...
    }
}); 
```

如果没有错误，设置外围设备上可用的主要服务:

```
bleno.setServices([
    ...
]); 
```

在阵列内部，创建一个新的主服务。这将接受您想要分配给服务的 UUID，以及一系列特征:

```
new bleno.PrimaryService({
    uuid : settings.service_id,
    characteristics : [
        ...
    ]

}); 
```

在存储特征的数组中，创建一个新的特征。它接受一个对象，该对象包含特性的默认值、要分配的 UUID、属性数组以及每个属性的实现方法。

```
new bleno.Characteristic({
  value : null,
  uuid : settings.characteristic_id,
  properties : ['write'],
  onWriteRequest : function(data, offset, withoutResponse, callback){
    var attendee = JSON.parse(data.toString());
    attendee.time_entered = Date.now();
    attendees.push(attendee);
    console.log(attendees);
    callback(this.RESULT_SUCCESS);
  }
}) 
```

属性可以是`read`、`write`、`writeWithoutResponse`、`notify`或`indicate`。根据您的需要，您可以使用其中任何一种的组合。对于这个考勤 app，你只需要`write`，那么对应的实现方法就是`onWriteRequest`。每次应用程序发出写请求时，都会将以下参数传递给此方法:

*   `data`:应用程序发送的数据以`Buffer`的形式接收，这就是为什么你需要调用`toString`方法将其转换回 JSON 字符串。
*   `offset`:如果你要发送的数据是一个字节序列。并且您只想为每个写请求传输几个字节，则使用偏移量来提取您需要的值。可以把它看作是分页在 BLE 的实现。偏移在本教程中没有用到，但是知道它是很好的。
*   `withoutResponse`:一个布尔值，表示写方法是否需要响应。
*   `callback`:负责向 app 发回响应的回调函数。调用`callback(this.RESULT_SUCCESS)`相当于`return "OK"`。这是之前在 app 中作为参数传递给`ble.write`方法的成功回调函数的值。

通过在终端上执行`node attendance.js`来运行外设。一旦终端开始发布广告，您应该会在终端上看到以下内容:

```
started.. 
```

## 运行应用程序

通过首先添加平台来运行应用程序:

```
cordova platform add android 
```

然后在您的设备或模拟器上运行它:

```
cordova run android 
```

## 后续步骤

就是这样！在本教程中，您学习了如何创建一个 Cordova 应用程序来与 BLE 外围设备对话。这只是开始。一般来说，使用 BLE 外设和蓝牙有很多可能性。因此，这里有几个建议可以让你学到的东西更进一步:

*   使用套接字，以便每次有人登记到房间时，所有其他之前登记过的人都会收到通知。你可以用 [Socket.io](http://socket.io/) 来实现这个。
*   您可以让应用程序充当外围设备，让 Raspberry Pi 充当中央模块，而不是让 Raspberry Pi 之类的设备充当外围设备。这允许中央模块无限期地扫描附近的设备，并确定谁参加了，他们在房间中的具体时间以及他们何时离开。你可以使用 [noble](https://github.com/sandeepmistry/noble) 和[蓝牙 LE cordova 插件](https://github.com/randdusing/cordova-plugin-bluetoothle)来实现这个。
*   在 Sitepoint 上查看其他关于蓝牙的[文章。](https://www.sitepoint.com/mobile/internet-of-things-mobile/)

我希望这篇教程为你提供了足够的知识，让你开始构建使用蓝牙低能耗技术的应用程序。如果你有其他项目想法，意见，或问题，请让我知道在评论中。

## 分享这篇文章