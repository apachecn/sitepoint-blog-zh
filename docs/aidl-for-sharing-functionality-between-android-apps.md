# 与 AIDL 共享 Android 应用程序之间的功能

> 原文：<https://www.sitepoint.com/aidl-for-sharing-functionality-between-android-apps/>

在构建 Android 应用程序时，你有时可能需要公开其他进程可以使用的功能。由于这些调用是在不同的进程之间进行的，它们不可能是一个简单的函数，因为一个 Android 进程不能访问另一个进程中的数据。

为了在进程之间传递数据，需要相应地对数据进行编组和解组。这种将数据编组和解组为基本类型的过程是为了让操作系统能够理解。如果手动完成，通过进程间通信(IPC)传递数据会很乏味，而且容易出错。Android 界面定义语言(AIDL)有助于解决这个问题。AIDL 通常有三个组成部分:

## 接口库

使用客户端和服务之间的 AIDL 定义接口。这个库将同时依赖于客户端和实现接口的服务。

## 服务

这将是一个单独的应用程序(apk ),实现由接口库定义的接口。

## 客户

客户端可以是一个单独的应用程序(apk ),它将使用接口库调用服务。

在这篇文章中，我们将实现这些组件，并看看它们是如何一起使用的。本教程的完整代码可以在 GitHub 上找到。

## 定义 AIDL

为了定义 AIDL，我们需要一个带有*的文件。aidl* 扩展。在本教程中，我们将把 AIDL 放在一个名为 *androidaidllibrary* 的独立库项目中。Android SDK 获取 AIDL 并生成一个由服务实现的“绑定器”。

AIDL 语法类似于 Java。您可以在您的 AIDL 定义中使用所有基本类型和对象，如“字符串”、“列表”和“地图”。如果你想定义你自己的对象，那么你可以，但是你需要提供一个实现`Parcelable`接口的类定义。

在这个例子中，我们将定义一个名为`IRemoteProductService`的接口，它有两个方法，`addProduct`和`getProduct`。要在 Android Studio 中创建 AIDL，选择*文件- >新建- > AIDL - > AIDL 文件*菜单选项，创建一个名为*iremoteprodiservice*的文件，并在其中添加以下代码。您需要更改包和导入名称以匹配您的应用程序:

```
// IRemoteProductService.aidl
package com.androidaidl.androidaidllibrary;

import com.androidaidl.androidaidllibrary.Product;

interface IRemoteProductService {

    void addProduct(String name , int quantity, float cost);
    Product getProduct(String name);
}
```

如果您尝试构建项目到目前为止，它将会失败，因为使用了自定义的`product`类型，还没有定义。要定义一个自定义类型，我们需要做两件事。首先定义实现`Parcelable`接口的`Product`类和定义`Product`的 *aidl* 文件。创建如下*Product.java*和 *Product.aidl* 文件(根据需要更改包名):

```
package com.androidaidl.androidaidllibrary;

import android.os.Parcel;
import android.os.Parcelable;

public class Product implements Parcelable {

    private String name;
    private int quantity;
    private float cost;

    public Product(String name, int quantity, float cost) {
        this.name = name;
        this.quantity = quantity;
        this.cost = cost;
    }

    private Product(Parcel in) {
        name = in.readString();
        quantity = in.readInt();
        cost = in.readFloat();
    }

    @Override
    public int describeContents() {
        return 0;
    }

    @Override
    public void writeToParcel(Parcel out, int flags) {
        out.writeString(name);
        out.writeInt(quantity);
        out.writeFloat(cost);
    }

    public static final Parcelable.Creator<Product> CREATOR
            = new Parcelable.Creator<Product>() {
        public Product createFromParcel(Parcel in) {
            return new Product(in);
        }

        public Product[] newArray(int size) {
            return new Product[size];
        }
    };

    public String getName() {
        return name;
    }

    public int getQuantity() {
        return quantity;
    }

    public float getCost() {
        return cost;
    }

    @Override
    public String toString() {
        return "Product{" +
                "name='" + name + '\'' +
                ", quantity=" + quantity +
                ", cost=" + cost +
                '}';
    }
}
```

```
// Product.aidl
package com.androidaidl.androidaidllibrary;

parcelable Product;
```

*Product.java*是一个简单的 Java 文件，它为`Product`定义了字段和函数，并实现了必要的方法。`writeToParcel`将类的字段写入包中，并创建一个名为`CREATOR`的静态对象，它可以从包中创建对象。

现在你应该能够构建项目，并在*构建/生成*文件夹中看到自动生成的*IRemoteProductService.java*。

## 实现 AIDL 接口。

一旦我们创建了接口库，我们需要在服务中实现接口。为此，我们将创建一个单独的应用程序。“单独的应用程序”是指单独的 apk 文件。这可以是一个新项目或同一项目中的一个模块。通过使用不同的 apk，调用将是进程间的，这就是 AIDL 的设计目的。

添加 *androidaidllibrary* 作为应用程序的依赖项，并创建一个服务，如下所示:

```
package com.androidaidl.androidaidlservice;

import android.app.Service;
import android.content.Intent;
import android.os.IBinder;
import android.os.RemoteException;

import com.androidaidl.androidaidllibrary.IRemoteProductService;
import com.androidaidl.androidaidllibrary.Product;

import java.util.ArrayList;
import java.util.List;
import java.util.Collections;

public class ProductService extends Service {

    @Override
    public void onCreate() {
        super.onCreate();
    }

    @Override
    public IBinder onBind(Intent intent) {
        // Return the interface
        return mBinder;
    }

    private final IRemoteProductService.Stub mBinder = new IRemoteProductService.Stub() {

        List <Product> products = Collections.synchronizedList(new ArrayList<Product>());

        @Override
        public void addProduct(String name, int quantity, float cost) throws RemoteException {
            //Add product called on the service.
            //Idealy you should store the product in a local data base
            //or in some remote service.
            //You can add that code here . We are just storing in In memory list.
            Product product = new Product(name, quantity, cost);
            products.add(product);
        }

        @Override
        public Product getProduct(String name) throws RemoteException {
            //getProduct product called on the service.
            //Idealy you should store the product in a local data base
            //or in some remote service. Hence the product should be fetched from there.
            //You can add that code here .
            //We are just storing in In memory list.So fetching from in memory list.
            for(Product product : products) {
                if(product.getName().equalsIgnoreCase(name)) {
                    return product;
                }
            }
            return null;
        }
    };

}
```

当我们构建*iremoteprodiservice . aidl*文件时，它生成了一个名为`IRemoteProductService.Stub()`的类，我们的绑定器需要实现这个类。上面我们实现了这一点，并将产品存储在内存映射中。服务在`onBind`函数中将这个绑定器返回给客户机。我们需要在 *AndroidManifest.xml* 中声明服务:

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.androidaidl.androidaidlservice">

    <application android:allowBackup="true" android:label="@string/app_name"
        android:icon="@mipmap/ic_launcher" android:theme="@style/AppTheme">

        <service android:name="com.androidaidl.androidaidlservice.ProductService">
            <intent-filter>
                <action android:name="com.androidaidl.androidaidlservice.ProductService" />
            </intent-filter>
        </service>
    </application>

</manifest>
```

## 从客户端进行 IPC 调用。

一旦我们构建了接口库和实现接口的服务，我们就可以使用服务了。为此，我们构建了一个包含`Activity`的客户端应用程序，它将通过 aidl 从该服务中添加和获取产品，并在 UI 中显示它们。创建一个依赖于 *androidaidllibrary* 的应用程序(出于本教程的目的，它可以在同一个项目中),其布局和活动如下

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin"
    tools:context="com.androidaidl.androidaidl.MainActivity"
    android:id="@+id/mainLayout">

    <LinearLayout android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:text="Product name"
        android:id="@+id/txtName"
        />

    <EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/edtName"
        android:ems="10"
        />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:text="Product Quantity"
        android:id="@+id/txtQuantity"
       />

    <EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:inputType="number"
        android:ems="10"
        android:id="@+id/edtQuantity"
        android:layout_below="@+id/editName"
        />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:text="Product Cost"
        android:id="@+id/txtCost"
       />

    <EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:inputType="numberDecimal"
        android:ems="10"
        android:id="@+id/edtCost"
        />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Add Product"
        android:id="@+id/btnAdd"
        />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?android:attr/textAppearanceMedium"
            android:text="Search Product"
            android:id="@+id/txtSearch"
            />

        <EditText
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:ems="10"
            android:id="@+id/edtSearchProduct"
            />

        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Search Product"
            android:id="@+id/btnSearch"

            />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?android:attr/textAppearanceMedium"
            android:text=""
            android:id="@+id/txtSearchResult"
            />
    </LinearLayout>

</RelativeLayout>
```

```
package com.androidaidl.androidaidl;

import android.app.Activity;
import android.content.ComponentName;
import android.content.Context;
import android.content.Intent;
import android.content.ServiceConnection;
import android.os.Bundle;
import android.os.IBinder;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;

import com.androidaidl.androidaidllibrary.IRemoteProductService;
import com.androidaidl.androidaidllibrary.Product;

public class MainActivity extends Activity {

    private IRemoteProductService service;
    private RemoteServiceConnection serviceConnection;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        connectService();

        Button addProduct = (Button)findViewById(R.id.btnAdd);
        addProduct.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                try {
                    if (service != null) {
                        String name = ((EditText) findViewById(R.id.edtName)).getText().toString();
                        int quatity = Integer.parseInt(((EditText) findViewById(R.id.edtQuantity)).getText().toString());
                        float cost = Float.parseFloat(((EditText) findViewById(R.id.edtCost)).getText().toString());

                        service.addProduct(name, quatity, cost);
                        Toast.makeText(MainActivity.this, "Product added.", Toast.LENGTH_LONG)
                                .show();
                    } else {
                        Toast.makeText(MainActivity.this, "Service is not connected", Toast.LENGTH_LONG)
                                .show();
                    }
                } catch (Exception e) {

                }
            }
        });

        Button searchProduct = (Button)findViewById(R.id.btnSearch);
        searchProduct.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                try {
                    if (service != null) {
                        String name = ((EditText) findViewById(R.id.edtSearchProduct)).getText().toString();
                        Product product = service.getProduct(name);
                        if(product != null) {
                            ((TextView) findViewById(R.id.txtSearchResult)).setText(product.toString());
                        } else {
                            Toast.makeText(MainActivity.this, "No product found with this name", Toast.LENGTH_LONG)
                                    .show();
                        }

                    } else {
                        Toast.makeText(MainActivity.this, "Service is not connected", Toast.LENGTH_LONG)
                                .show();
                    }
                } catch(Exception e) {

                }
            }
        });

    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        // Inflate the menu; this adds items to the action bar if it is present.
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        // Handle action bar item clicks here. The action bar will
        // automatically handle clicks on the Home/Up button, so long
        // as you specify a parent activity in AndroidManifest.xml.
        int id = item.getItemId();

        //noinspection SimplifiableIfStatement
        if (id == R.id.action_settings) {
            return true;
        }

        return super.onOptionsItemSelected(item);
    }

    private void connectService() {
        serviceConnection = new RemoteServiceConnection();
        Intent i = new Intent("com.androidaidl.androidaidlservice.ProductService");
        i.setPackage("com.androidaidl.androidaidlservice");
        boolean ret = bindService(i, serviceConnection, Context.BIND_AUTO_CREATE);
    }
    class RemoteServiceConnection implements ServiceConnection {

        public void onServiceConnected(ComponentName name, IBinder boundService) {
            service = IRemoteProductService.Stub.asInterface((IBinder) boundService);
            Toast.makeText(MainActivity.this, "Service connected", Toast.LENGTH_LONG)
                    .show();
        }

        public void onServiceDisconnected(ComponentName name) {
            service = null;
            Toast.makeText(MainActivity.this, "Service disconnected", Toast.LENGTH_LONG)
                    .show();
        }
    }
}
```

在上面的活动的`onCreate`方法中，我们调用`connectService`,它为服务创建意图并调用`bindService`,传递服务连接时调用的`ServiceConnection`的实现。当服务被连接时，绑定器作为回调返回，回调是我们存储的用于调用的接口的实现。

在 UI 中，我们创建字段来输入添加产品的值，并对存储的服务对象调用`addProduct`。为了获取产品，我们称之为`getProduct`。安装两个 apk(服务和客户端活动),您应该能够看到屏幕并添加和获取产品，如下所示。

## 结论

Android 使得进程间的通信变得简单。它提供了删除大量样板代码的工具和机制，否则这些代码将是必需的。

AIDL 提供了一种干净简单的方法来在 Android 中构建组件，以便在应用程序和服务之间共享功能。

如果你有任何疑问或问题，请告诉我。

## 分享这篇文章