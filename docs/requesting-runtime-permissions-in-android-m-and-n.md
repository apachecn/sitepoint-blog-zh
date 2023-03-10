# 在 Android M 和 N 中请求运行时权限

> 原文：<https://www.sitepoint.com/requesting-runtime-permissions-in-android-m-and-n/>

从 Android Marshmallow (API 23)开始，应用程序运行时会询问用户权限。这样，用户能够选择他们应该授予哪些权限，而不会影响应用程序流。在本教程中，我将介绍在 Android M 和 N 中请求运行时权限，如何执行请求，获得结果，然后处理它。

![Requesting a Permission](img/c147a6f621f1c8378cb35132d88af2b4.png)

安卓系统中有很多`user-permissions`，但我将只关注一些最常用的。

你可以在 [github](https://github.com/sitepoint-editors/RuntimePermissions) 中找到这个项目的代码。

首先，在 Android Studio 中创建一个新项目，选择最低 API 级别 *23* 并添加一个*空活动*。这将是项目中唯一的活动。

## 声明权限

打开 *AndroidManifest.xml* 并添加以下权限:

```
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

<uses-permission android:name="android.permission.CALL_PHONE"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>

<uses-permission android:name="android.permission.CAMERA"/>

<uses-permission android:name="android.permission.GET_ACCOUNTS"/> 
```

上面的权限是 android 应用程序中最常用的一些，但它们都以类似的方式工作。

## 请求许可

将通过使用一个按钮来询问每个权限。将 *activity_main.xml* 中的代码更新为:

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
    tools:context="com.example.theodhor.runtimepermissions.MainActivity">

    <LinearLayout
        android:orientation="vertical"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true">

        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Location"
            android:id="@+id/location"
            android:layout_marginBottom="10dp"
            android:onClick="ask" />

        <Button
            style="?android:attr/buttonStyleSmall"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Write ExStorage"
            android:id="@+id/write"
            android:onClick="ask"
            android:layout_marginBottom="10dp" />

        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Read ExStorage"
            android:id="@+id/read"
            android:layout_marginBottom="10dp"
            android:onClick="ask" />

        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Call"
            android:id="@+id/call"
            android:layout_marginBottom="10dp"
            android:onClick="ask" />

        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Camera"
            android:id="@+id/camera"
            android:layout_marginBottom="10dp"
            android:onClick="ask" />

        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Get Accounts"
            android:id="@+id/accounts"
            android:layout_marginBottom="10dp"
            android:onClick="ask" />
    </LinearLayout>

</RelativeLayout> 
```

这是您刚刚创建的布局:

![Layout](img/af88494340dd9d37b9bebacfcc88333c.png)

在此阶段，如果用户安装了应用程序，它将需要以下权限:

![Permissions Required](img/c228e56d8a2dc2f0133aca115a82c0fe.png)

在*MainActivity.java*里面，在`onCreate`之后加上这个方法:

```
private void askForPermission(String permission, Integer requestCode) {
    if (ContextCompat.checkSelfPermission(MainActivity.this, permission) != PackageManager.PERMISSION_GRANTED) {

        // Should we show an explanation?
        if (ActivityCompat.shouldShowRequestPermissionRationale(MainActivity.this, permission)) {

            //This is called if user has denied the permission before
            //In this case I am just asking the permission again
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{permission}, requestCode);

        } else {

            ActivityCompat.requestPermissions(MainActivity.this, new String[]{permission}, requestCode);
        }
    } else {
        Toast.makeText(this, "" + permission + " is already granted.", Toast.LENGTH_SHORT).show();
    }
} 
```

此方法向用户询问权限。首先，它检查您请求的权限是否被授予。如果是，那么应用程序会显示一个 *Toast* ，表示许可已经被授予。如果该权限未被授予，它将检查用户以前是否拒绝过该权限。如果权限对应用程序很重要，应该再次请求。如果之前没有拒绝许可，通过调用`ActivityCompat.requestPermissions(MainActivity.this, new String[]{permission}, requestCode);`执行请求

每个权限请求需要三个参数。第一个是`context`，第二个是权限的`String array`，第三个是类型`Integer`的`requestCode`。最后一个参数是附加到请求的随机代码，可以是适合您的用例的任何数字。当结果在活动中返回时，它包含此代码，并使用它来区分多个结果。

该方法已经准备好执行请求，现在它应该与相应的按钮相链接。创建的每个按钮都有一个`android:onClick="ask"`属性。

您需要创建一个名为`ask`的`public`方法，每次点击按钮时都会调用该方法。它的代码是:

```
public void ask(View v){
    switch (v.getId()){
        case R.id.location:
            askForPermission(Manifest.permission.ACCESS_FINE_LOCATION,LOCATION);
            break;
        case R.id.call:
            askForPermission(Manifest.permission.CALL_PHONE,CALL);
            break;
        case R.id.write:
            askForPermission(Manifest.permission.WRITE_EXTERNAL_STORAGE,WRITE_EXST);
            break;
        case R.id.read:
            askForPermission(Manifest.permission.READ_EXTERNAL_STORAGE,READ_EXST);
            break;
        case R.id.camera:
            askForPermission(Manifest.permission.CAMERA,CAMERA);
            break;
        case R.id.accounts:
            askForPermission(Manifest.permission.GET_ACCOUNTS,ACCOUNTS);
            break;
        default:
            break;
    }
} 
```

`askForPermission`方法的第二个参数是一些随机整数。在`onCreate()`方法之前添加它们的声明:

```
static final Integer LOCATION = 0x1;
static final Integer CALL = 0x2;
static final Integer WRITE_EXST = 0x3;
static final Integer READ_EXST = 0x4;
static final Integer CAMERA = 0x5;
static final Integer ACCOUNTS = 0x6;
static final Integer GPS_SETTINGS = 0x7; 
```

现在应用程序可以执行请求了。下一步是处理请求结果。

## 处理结果

为了处理权限请求的结果，调用了`onRequestPermissionsResult`方法。它的代码如下:

```
@Override
public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    if(ActivityCompat.checkSelfPermission(this, permissions[0]) == PackageManager.PERMISSION_GRANTED){
        switch (requestCode) {
            //Location
            case 1:
                askForGPS();
                break;
            //Call
            case 2:
                Intent callIntent = new Intent(Intent.ACTION_CALL);
                callIntent.setData(Uri.parse("tel:" + "{This is a telephone number}"));
                if (ActivityCompat.checkSelfPermission(this, Manifest.permission.CALL_PHONE) != PackageManager.PERMISSION_GRANTED) {
                    startActivity(callIntent);
                }
                break;
            //Write external Storage
            case 3:
                break;
            //Read External Storage
            case 4:
                Intent imageIntent = new Intent(Intent.ACTION_PICK, android.provider.MediaStore.Images.Media.EXTERNAL_CONTENT_URI);
                startActivityForResult(imageIntent, 11);
                break;
            //Camera
            case 5:
                Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
                if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
                    startActivityForResult(takePictureIntent, 12);
                }
                break;
            //Accounts
            case 6:
                AccountManager manager = (AccountManager) getSystemService(ACCOUNT_SERVICE);
                Account[] list = manager.getAccounts();
                Toast.makeText(this,""+list[0].name,Toast.LENGTH_SHORT).show();
                for(int i=0; i<list.length;i++){
                    Log.e("Account "+i,""+list[i].name);
                }
        }

        Toast.makeText(this, "Permission granted", Toast.LENGTH_SHORT).show();
    }else{
        Toast.makeText(this, "Permission denied", Toast.LENGTH_SHORT).show();
    }
} 
```

当返回结果时，它检查是否授予了权限。如果是，则将`requestCode`传递给`switch`语句以区分结果。我在每个`case`中添加了额外的行来显示进一步的步骤，但它们只是例子。

## 位置请求

在`case 1:`有一个函数叫做`askForGPS()`。如果 GPS 未启用，此功能会提示用户启用它。如果用户授予定位权限后设备未连接到 GPS，将显示以下对话框:

![No GPS dialog](img/fe6812e594befbaac93fd5c5bf98cb20.png)

要显示这个对话框，您需要一个`GoogleApiClient`。首先，通过在`onCreate`前添加这些行来声明一些变量:

```
GoogleApiClient client;
LocationRequest mLocationRequest;
PendingResult<LocationSettingsResult> result; 
```

并在`onCreate`方法中构建`client`:

```
client = new GoogleApiClient.Builder(this)
    .addApi(AppIndex.API)
    .addApi(LocationServices.API)
    .build(); 
```

现在客户端已经构建好了，它需要在应用程序启动时连接，在应用程序停止时断开。将这两个`overriden`方法添加到`MainActivity`:

```
@Override
public void onStart() {
    super.onStart();
    client.connect();
}

@Override
public void onStop() {
    super.onStop();
    client.disconnect();
} 
```

最后，添加显示 GPS 对话框的函数:

```
private void askForGPS(){
    mLocationRequest = LocationRequest.create();
    mLocationRequest.setPriority(LocationRequest.PRIORITY_HIGH_ACCURACY);
    mLocationRequest.setInterval(30 * 1000);
    mLocationRequest.setFastestInterval(5 * 1000);
    LocationSettingsRequest.Builder builder = new LocationSettingsRequest.Builder().addLocationRequest(mLocationRequest);
    builder.setAlwaysShow(true);
    result = LocationServices.SettingsApi.checkLocationSettings(client, builder.build());
    result.setResultCallback(new ResultCallback<LocationSettingsResult>() {
        @Override
        public void onResult(LocationSettingsResult result) {
            final Status status = result.getStatus();
            switch (status.getStatusCode()) {
                case LocationSettingsStatusCodes.SUCCESS:
                    break;
                case LocationSettingsStatusCodes.RESOLUTION_REQUIRED:
                    try {
                        status.startResolutionForResult(MainActivity.this, GPS_SETTINGS);
                    } catch (IntentSender.SendIntentException e) {

                    }
                    break;
                case LocationSettingsStatusCodes.SETTINGS_CHANGE_UNAVAILABLE:
                    break;
            }
        }
    });
} 
```

## 结论

在本教程中，我展示了如何请求权限，并使用 Android 的新权限模型处理结果。这种新模式的目的是让用户控制我们的应用程序需要做什么。很想听听大家对这个变化的评论和看法，以及如何在下面处理。

## 分享这篇文章