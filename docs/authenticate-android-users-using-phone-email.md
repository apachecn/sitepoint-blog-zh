# 如何使用电话或电子邮件认证您的 Android 用户

> 原文：<https://www.sitepoint.com/authenticate-android-users-using-phone-email/>

在本教程中，我将展示如何使用脸书的帐户工具包工具，让用户登录或注册使用他们的电话号码或电子邮件。你可以在 [Github](https://github.com/sitepoint-editors/AccountKit) 上找到已经完成的项目。

![AccountKit Activities](img/40481f328f424d17a39828c84713aed1.png)

创建您的项目后，请确保将您的应用程序添加到脸书开发者[控制台](https://developers.facebook.com/apps/)并获取您的**应用程序 ID** 。

一旦你的 Android Studio 项目设置好了，进入你的`Project/build.gradle`，在`repositories{ }`中添加`mavenCentral()`

另外，打开您的`app/build.gradle`文件并添加帐户套件依赖关系:

`compile 'com.facebook.android:account-kit-sdk:4.20.0'`

## Android 清单文件和字符串

在`strings.xml`文件中，添加以下 id:

```
 <string name="facebook_app_id">YOUR_FACEBOOK_APP_ID</string>
    <string name="ACCOUNT_KIT_CLIENT_TOKEN">YOUR_ACCOUNT_KIT_CLIENT_TOKEN</string>
    <string name="ak_login_protocol_scheme">akYOUR_FACEBOOK_APP_ID</string> 
```

接下来，我们需要在`AndroidManifest.xml` :
中声明所需的权限、活动和元数据，因为这个应用程序需要互联网来执行电话或电子邮件登录，所以确保在 Manifest 文件的顶部添加了互联网权限。

```
<uses-permission android:name="android.permission.INTERNET"/> 
```

为了正确配置我们的应用程序并与脸书仪表板连接，我们需要添加以下元数据:

```
<meta-data android:name="com.facebook.accountkit.ApplicationName"
            android:value="@string/app_name" />
<meta-data android:name="com.facebook.sdk.ApplicationId"
    android:value="@string/facebook_app_id" />
<meta-data android:name="com.facebook.accountkit.ClientToken"
    android:value="@string/ACCOUNT_KIT_CLIENT_TOKEN" />
<meta-data android:name="com.facebook.accountkit.FacebookAppEventsEnabled"
    android:value="false"/> 
```

在`<application>`标签中，我们需要声明电子邮件重定向活动。

```
<activity
    android:name="com.facebook.accountkit.ui.AccountKitEmailRedirectActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="@string/ak_login_protocol_scheme" />
            </intent-filter>
 </activity> 
```

此活动检测您何时单击电子邮件正文中的帐户工具包类链接或 URL，并启动应用程序。当用户确认收到关于使用 Account Kit 登录电子邮件的电子邮件时，就会发生这种情况。

在本教程中，我们将使用一个非常简单的布局。它只包含三个按钮，您可以在下面找到它的代码:

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    tools:context="com.example.theodhor.fbaccountkit.MainActivity">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:onClick="emailLogin"
        android:text="Email Login"
        android:textAllCaps="false"/>

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Phone Login"
        android:textAllCaps="false"
        android:onClick="phoneLogin"/>

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Logout"
        android:textAllCaps="false"
        android:onClick="logout"/>

</LinearLayout> 
```

前两个按钮中的每一个都将打开一个新视图，电话号码登录类型或电子邮件，具体取决于具体情况。第三个按钮清除存储的会话。

## 使用电子邮件或电话登录

首先，我们将如下声明两个全局变量:

```
public static int APP_REQUEST_CODE = 99;
public static final String TAG = "MainActivity"; 
```

在`xml`中声明的每个按钮都有一个作为`onClick`属性参数的功能。
所以我们需要在活动中使用`public void emailLogin(View view)`方法。

```
public void emailLogin(@Nullable View view) {
    final Intent intent = new Intent(this, AccountKitActivity.class);
    AccountKitConfiguration.AccountKitConfigurationBuilder configurationBuilder = new AccountKitConfiguration.AccountKitConfigurationBuilder(
            LoginType.EMAIL,AccountKitActivity.ResponseType.TOKEN); // or .ResponseType.CODE
    UIManager uiManager = new SkinManager(
            LoginType.EMAIL,
            SkinManager.Skin.CLASSIC,
            (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M ? getResources().getColor(R.color.colorPrimary,null):getResources().getColor(R.color.colorPrimary)),
            R.drawable.background,
            SkinManager.Tint.BLACK,
            0.55
    );
    configurationBuilder.setUIManager(uiManager);
    intent.putExtra(AccountKitActivity.ACCOUNT_KIT_ACTIVITY_CONFIGURATION,configurationBuilder.build());
    startActivityForResult(intent, APP_REQUEST_CODE);
} 
```

此方法将打开电子邮件登录视图。`@Nullable`注释允许我们调用同一个方法，即使有一个`null`参数。

它的 UI 将由`UIManager`对象决定。使用此对象，您可以自定义其皮肤类型、原色、背景图像、色调颜色和色调强度。

要启动电话登录类型活动，只需设置 *LoginType。发送电子邮件*至 *LoginType。电话*

Account Kit 活动结束后，它将同时返回一个带有 *AccessToken* 的 *AccountKit 账户*。为了获得这个帐户和访问令牌，我们需要覆盖`onActivityResult`方法。它的代码如下:

```
@Override
    protected void onActivityResult(final int requestCode,final int resultCode,final Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == APP_REQUEST_CODE && resultCode == RESULT_OK) {
            getCurrentAccount();
        }
    } 
```

`getCurrentAccount();`方法:

```
private void getCurrentAccount(){
        AccessToken accessToken = AccountKit.getCurrentAccessToken();
        if (accessToken != null) {
            //Handle Returning User
            AccountKit.getCurrentAccount(new AccountKitCallback<Account>() {
                @Override
                public void onSuccess(final Account account) {
                    // Get Account Kit ID
                    String accountKitId = account.getId();
                    Log.e("Account Kit Id", accountKitId);

                    if(account.getPhoneNumber()!=null) {
                        Log.e("CountryCode", "" + account.getPhoneNumber().getCountryCode());
                        Log.e("PhoneNumber", "" + account.getPhoneNumber().getPhoneNumber());

                        // Get phone number
                        PhoneNumber phoneNumber = account.getPhoneNumber();
                        String phoneNumberString = phoneNumber.toString();
                        Log.e("NumberString", phoneNumberString);
                    }

                    if(account.getEmail()!=null)
                        Log.e("Email",account.getEmail());
                }

                @Override
                public void onError(final AccountKitError error) {
                    // Handle Error
                    Log.e(TAG,error.toString());
                }
            });
        } else {
            //Handle new or logged out user
            Log.e(TAG,"Logged Out");
        }
    } 
```

如果 AccountKit 活动的结果没问题，那么我们将拥有一个 *AccessToken* 和一个 *AccountKit 帐户*。根据登录类型，我们可以获得用户的电子邮件或电话，甚至两者都有。无论使用哪种登录类型，结果都是相同的。

要检查用户是否已经使用 AccountKit 登录，您也可以将`getCurrentAccount()`放在`onCreate()`方法中，它将检查是否存在 AccountKit AccessToken。

第三个按钮将注销当前用户并清除任何保存的访问令牌。其代码如下:

```
public void logout(@Nullable View view){
        AccountKit.logOut();
        AccessToken accessToken = AccountKit.getCurrentAccessToken();
        if(accessToken!=null)
            Log.e(TAG,"Still Logged in...");
        else
            Toast.makeText(this,"Logged Out",Toast.LENGTH_SHORT).show();
    } 
```

这些是使用脸书帐户工具包作为替代登录方法时要记住的最重要的方法。

## 结论

脸书帐户工具包是一个非常方便的工具，更简单，更快，无密码的应用程序登录或注册。这是同类服务中最可靠的服务之一。希望你喜欢这篇文章。

如果您有任何问题或意见，请在下面的评论栏中告诉我。

## 分享这篇文章