# 如何为 Android 开发编写测试

> 原文：<https://www.sitepoint.com/write-tests-android-development/>

Android 应用的自动化单元测试对于其长期质量是必要的。单元测试有助于测试代码的一个单元(例如一个类)。这有助于在开发周期的早期捕捉和识别 bug 或退化。在这篇文章中，我们将看到如何为我们的 Android 应用程序编写单元测试。在 Android 中，单元测试有两种类型:

*   本地单元测试——在开发机器上运行，而不是在实际的机器上
*   测试单元测试——在实际的 Android 设备上运行。

## 用 JUnit 创建单元测试，用 Mockito 模仿对象

我们将从为添加两个数的 Android 应用程序创建本地单元测试开始。
该应用包含以下助手类

```
package com.testsinandroid;

import android.support.annotation.VisibleForTesting;

public class NumberAdder {

       private final MainActivity mMainActivity;
       public NumberAdder(MainActivity activity) {
            mMainActivity = activity;
        }

    public void performAddition() {

        double number1 = mMainActivity.getFirstNumber();
        double number2 = mMainActivity.getSecondNumber();

        if(!isNumberValid(number1) || !isNumberValid(number2)) {
            throw new RuntimeException("invalid numbers");
        }

        double result = number1 + number2;

        mMainActivity.setAdditionResult(result);
    }

    @VisibleForTesting
    boolean isNumberValid(double number) {
        if(number > 0) {
            return true;
        } else {
            return false;
        }
    }
} 
```

并且还包含使用该类的活动

```
package com.testsinandroid;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;

public class MainActivity extends Activity {

    EditText firstNumber;
    EditText secondNumber;
    TextView addResult;
    Button btnAdd;

    NumberAdder numberAdder = null;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        firstNumber = (EditText)findViewById(R.id.txtNumber1);
        secondNumber = (EditText)findViewById(R.id.txtNumber2);
        addResult = (TextView)findViewById(R.id.txtResult);
        btnAdd = (Button)findViewById(R.id.btnAdd);

        if(numberAdder == null) {
            numberAdder = new NumberAdder(this);
        }

        btnAdd.setOnClickListener(new View.OnClickListener() {
            public void onClick(View v) {
                numberAdder.performAddition();
            }
        });

    }

    public double getFirstNumber() {
        return Double.parseDouble(firstNumber.getText().toString());
    }

    public double getSecondNumber() {
        return Double.parseDouble(secondNumber.getText().toString());
    }

    public void setAdditionResult(double result) {
        addResult.setText(Double.toString(result));
    }

} 
```

活动布局如下

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">

    <EditText
        android:id="@+id/txtNumber1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="2"
        android:inputType="number" >

        <requestFocus />
    </EditText>

    <EditText
        android:id="@+id/txtNumber2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@+id/txtNumber1"
        android:ems="2"
        android:inputType="number" >

    </EditText>

    <Button
        android:id="@+id/btnAdd"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="46dp"
        android:layout_below="@+id/txtNumber2"
        android:text="Add" />

    <TextView
        android:id="@+id/txtResult"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@+id/btnAdd"
        android:textAppearance="?android:attr/textAppearanceMedium" />
</RelativeLayout> 
```

一旦我们准备好了我们的应用程序，我们现在就有两个主要的单元来测试我们的代码

*   number adder——这是一个普通的 Java 类，将 MainActivity 作为依赖项。
*   main activity–显示用户界面的活动。

因为`NumberAdder`是一个普通的 Java 类，所以可以使用 [Junit](http://junit.org/) 来测试这样的类。JUnit 是一个编写可重复单元测试的简单框架。当我们测试一个单元(在这个例子中是 NumberAdder)时，NumberAdder 所依赖的所有其他类都可以被模拟出来。在 Java 中模仿依赖关系的一个很好的框架是 [Mockito](http://site.mockito.org/) 。要将 JUnit 和 Mockito 作为测试依赖项添加到我们的项目中，请将以下内容添加到 build.gradle

```
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:25.0.1'

    // Required -- JUnit 4 framework
    testCompile 'junit:junit:4.12'
    testCompile 'org.mockito:mockito-core:1.10.19'

} 
```

要为 NumberAdder 编写一个 Junit + Mockito 测试，请在文件夹
src/test/Java/com/testsinandroid 中创建一个文件 NumberAdderTest.java，内容如下

```
package com.testsinandroid;
import com.testsinandroid.MainActivity;
import com.testsinandroid.NumberAdder;

import org.junit.Test;
import static org.junit.Assert.assertFalse;
import static org.junit.Assert.assertTrue;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.*;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.Mock;
import org.mockito.runners.MockitoJUnitRunner;

@RunWith(MockitoJUnitRunner.class)
public class NumberAdderTest {

    @Mock
    MainActivity mMockMainActivity;

    @Test
    public void testIsNumberValid() {
        //setup

        //test
        NumberAdder numberAdder = new NumberAdder(mMockMainActivity);
        assert(numberAdder.isNumberValid(55.0));

    }

    @Test
    public void testIsNumberNotValid() {
        //setup

        //test
        NumberAdder numberAdder = new NumberAdder(mMockMainActivity);
        assertFalse(numberAdder.isNumberValid(-55.0));

    }

    @Test
    public void testPerformAddition() {
        //setup
        when(mMockMainActivity.getFirstNumber())
                .thenReturn(10.0);
        when(mMockMainActivity.getSecondNumber())
                .thenReturn(11.0);

        //test
        NumberAdder numberAdder = new NumberAdder(mMockMainActivity);
        numberAdder.performAddition();

        //verify
        verify(mMockMainActivity).setAdditionResult(21.0);

    }

} 
```

上面的代码创建了三个测试，并用@Test 注释进行了注释。测试是使用 MockitoJUnitRunner 运行的。这个 runner 为每个用@mock 注释的字段注入一个 Mock 对象。上面的测试模拟了 MainActivity。在前两个测试中，我们基于函数返回值进行断言。在第三个测试中，我们设置了调用 getFirstNumber 和 getSecondNumber 时要返回的值。然后，我们验证方法 setAdditionResult 在 mMockMainActivity 上是否有正确的值。

一旦我们编写了这些本地 JUnit 测试，我们就可以使用 gradle 命令运行它们。如果所有的测试都通过了，那么构建就成功了，否则就失败了。

```
./gradlew test 
```

## 使用 Roboelectric 编写测试

Robolectric 是一个用于 Android 的单元测试框架。使用 Roboelectric，您可以在工作站上的 JVM 上运行 android 单元测试。这对于在您的工作站上对您的 Android 代码进行单元测试变得非常方便。如果测试没有通过，构建可能会失败。

要将 Roboelectric 添加到您的测试依赖项中，请按如下方式更新您的依赖项

```
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:25.0.1'

    // Add Junit and mockito as testing dependencies
    testCompile 'junit:junit:4.12'
    testCompile 'org.mockito:mockito-core:1.10.19'

    //For Roboelectric
    testCompile "org.robolectric:robolectric:3.0"
} 
```

一旦我们添加了依赖项，我们就可以为 MainActivity 添加以下测试

```
package com.testsinandroid;

import org.junit.Test;
import org.junit.Before;

import org.junit.runner.RunWith;
import org.robolectric.Robolectric;
import org.robolectric.annotation.Config;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import static org.junit.Assert.*;

import static org.junit.Assert.assertTrue;
import org.robolectric.RobolectricGradleTestRunner;

@RunWith(RobolectricGradleTestRunner.class)
@Config(constants = BuildConfig.class, sdk = 21)
public class MainActivityTest {

    MainActivity activity;
    EditText firstNumber;
    EditText secondNumber;
    TextView addResult;
    Button btnAdd;

    @Before
    public void setUp() {
        activity = Robolectric.setupActivity(MainActivity.class);
        firstNumber = (EditText)activity.findViewById(R.id.txtNumber1);
        secondNumber = (EditText)activity.findViewById(R.id.txtNumber2);
        addResult = (TextView)activity.findViewById(R.id.txtResult);
        btnAdd = (Button) activity.findViewById(R.id.btnAdd);
    }

    @Test
    public void testMainActivityAddition() {
        //setup 

        firstNumber.setText("12.2");
        secondNumber.setText("13.3");

        //test
        btnAdd.performClick();

        //verify
        assertEquals(25.5, Double.parseDouble(addResult.getText().toString()), 0.0);

    }
} 
```

在上面的测试中，我们创建了 MainActivityTest。它是用机器人来运行的。然后我们使用 robo electric API‘robolectric . setup activity’来实例化活动。它还调用它的生命周期方法，如“OnCreate”、“onStart”等。然后，我们在活动中获取不同的视图元素并设置值。设置好值后，我们单击按钮。然后，我们最终通过使用“assertEquals”来验证结果 TextView 具有适当的值。

如上面的例子所示，Roboelectric 使得为你的 Android 代码编写单元测试变得很容易。

## 编写 Android 工具测试

Android 让你编写仪器单元测试。这些是在真实的 Android 设备上运行的测试。因为您是在实际设备上测试，所以您不需要模仿 Android 类。使用仪器测试来测试 android 组件，如 activity 等变得很容易。但是问题是你需要一个实际的设备来运行测试。这些不能在你的工作站上运行。

要添加检测，请按如下方式更新您的梯度依赖项

```
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:25.0.1'

    // Add Junit and mockito as testing dependencies
    testCompile 'junit:junit:4.12'
    testCompile 'org.mockito:mockito-core:1.10.19'

    //For Roboelectric
    testCompile "org.robolectric:robolectric:3.0"

    //For Instrumentation tests

    androidTestCompile 'com.android.support:support-annotations:25.0.1'
    androidTestCompile 'com.android.support.test:runner:0.5'
    androidTestCompile 'com.android.support.test:rules:0.5'
} 
```

要编写“主活动”的仪表测试，请创建一个文件 MainActivityIntrumentationTest.java。该文件应该在文件夹
src/androidTest/Java/com/testsinandroid 中，内容如下

```
package com.testsinandroid;

import android.app.Instrumentation;
import android.test.ActivityInstrumentationTestCase2;
import android.test.TouchUtils;
import android.test.ViewAsserts;
import android.view.View;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import static org.junit.Assert.*;
import android.test.UiThreadTest; 

public class MainActivityIntrumentationTest extends ActivityInstrumentationTestCase2<MainActivity> {
    MainActivity activity;
    EditText firstNumber;
    EditText secondNumber;
    TextView addResult;
    Button btnAdd;

  public MainActivityIntrumentationTest() {
    super(MainActivity.class);
  }

  @Override
  protected void setUp() throws Exception {
    super.setUp();

    setActivityInitialTouchMode(true);

    activity = getActivity();
    firstNumber = (EditText)activity.findViewById(R.id.txtNumber1);
    secondNumber = (EditText)activity.findViewById(R.id.txtNumber2);
    addResult = (TextView)activity.findViewById(R.id.txtResult);
    btnAdd = (Button) activity.findViewById(R.id.btnAdd);
  }

   @UiThreadTest
   public void testMainActivityAddition() {

        //setup 
        firstNumber.setText("12.2");
        secondNumber.setText("13.3");

        //test
        btnAdd.performClick();

        //verify
        assertEquals(25.5, Double.parseDouble(addResult.getText().toString()), 0.0);

   }

} 
```

在上面的代码中，我们的测试类继承自 activityinstrumentationtestcase 2。activityinstrumentationtestcase 2 让我们为活动编写测试。在这个类的构造函数中，我们应该传递测试中的活动(在我们的例子中是“MainActivity”)。然后，我们重写“setUp”方法，在该方法中，我们获取活动对象和活动的其他 UI 元素。然后，我们编写一个测试，它类似于我们在上一节中编写的测试。该测试有一个批注“@UiThreadTest”。这将使这个测试在 UI 线程上运行，因为我们在测试中有一些 UI 操作。使用命令连接设备后，我们可以运行仪器测试

```
./gradlew connectedCheck 
```

## 结论

在本文中，我们看到了许多为 Android 代码编写单元测试的方法。根据您对项目的需求，您可以在您的项目中进行一种或多种类型的上述测试。自动化单元测试有很多长期的好处。它们应该被认为是开发工作本身的一部分。上述框架有助于为你的 android 应用编写单元测试。他们为你做了大部分繁重的工作，所以你可以专注于测试。所以，祝你在下一个 Android 应用中编写单元测试愉快。

## 分享这篇文章