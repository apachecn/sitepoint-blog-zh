# 在你的应用中使用 Android 传感器

> 原文：<https://www.sitepoint.com/using-android-sensors-application/>

监控应用程序越来越受欢迎，从希望跟踪孩子位置和在线活动的父母，到希望监控各自配偶和员工的配偶和雇主。在这篇文章中，我们将偏离道德和伦理问题，把重点放在技术方面。我们以安卓传感器为例。

Android 传感器返回的值可以分为三大类:运动、位置和环境。

加速度计是一种测量适当加速度(或“重力”)的设备，通常用于检测运动(抖动和倾斜)。在移动监控中，加速度计可用于监控独居老人。加速度计监控下落速度以检测人是否摔倒。如果检测到跌倒，管理员会收到一条信息警报。

[根据 Dan Morrill](http://android-developers.blogspot.de/2010/09/one-screen-turn-deserves-another.html) (谷歌 Android 兼容性和开源项目经理)的说法，要正确使用 API，你应该遵循以下三条规则:

*   传感器坐标系与 OpenGL 坐标系相同，如果使用相同的 API 进行自然定向，则不会改变。
*   自然取向并不总是纵向的。
*   `android.view.Display.getRotation()`应始终由将传感器数据与屏幕显示相匹配的应用程序使用，以便将传感器坐标与屏幕相匹配。

## 让我们看看它是如何工作的

当传感器值改变时，我们将使用`SensorEventListener`接收来自`SensorManager`的通知。因为我们要使用摇动手势，所以锁定设备的方向是个好主意。

在 IDE 中创建一个新项目，并将以下内容添加到您的`AndroidManifest.xml`文件中:

```
android:screenOrientation="portrait"
```

用以下内容替换`MainActivity.java`的内容:

```
public class MainActivity extends Activity implements SensorEventListener {

    private final String TAG = MainActivity.class.getSimpleName();
    private SensorManager mSensorManager;
    private Sensor mAccelerometer;
    private long lastTime = 0;
    private float lastX, lastY, lastZ;
    private static final int THRESHOLD = 600; //used to see whether a shake gesture has been detected or not.
    TextView coordinates;
    TextView address;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mSensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);
        mAccelerometer = mSensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
        mSensorManager.registerListener(this, mAccelerometer, SensorManager.SENSOR_DELAY_NORMAL);
        Button update = (Button) findViewById(R.id.update_button);
        update.setOnClickListener(new UpdateLocationClick());
        coordinates = (TextView) findViewById(R.id.location_points);
        address = (TextView) findViewById(R.id.location_address);
        if (SApplication.LOCATION != null) {
            double lat = SApplication.LOCATION.getLatitude();
            double lon = SApplication.LOCATION.getLongitude();
            coordinates.setText(lat + " " + lon);
            Geocoder geocoder = new Geocoder(getApplicationContext(), new Locale("en"));
            try {
                List<Address> addresses = geocoder.getFromLocation(lat, lon, 1);
                if (addresses != null && addresses.size() != 0) {
                    StringBuilder builder = new StringBuilder();
                    Address returnAddress = addresses.get(0);
                    for (int i = 0; i < returnAddress.getMaxAddressLineIndex(); i++) {
                        builder.append(returnAddress.getAddressLine(i));
                        builder.append(" ");
                    }
                    address.setText(builder);
                    address.setVisibility(View.VISIBLE);
                } else {
                    Log.e(TAG, "Addresses null");
                }
            } catch (IOException e) {
                Log.e(TAG, "Geocoder exception " + e);
            }
        } else {
            coordinates.setText("No location yet");
            address.setVisibility(View.INVISIBLE);
        }
    }
}
```

该系统的传感器很灵敏。当你拿着一个设备时，不管你的手有多稳，它总是在运动。我们不需要收集所有这些数据。我们存储系统的当前时间(以毫秒为单位),并检查自上次调用`onSensorChanged`以来是否已经过了 100 毫秒。

将它添加到`MainActivity`类中的`MainActivity.java`:

```
@Override
    public void onSensorChanged(SensorEvent event) {
        Sensor sensor = event.sensor;
        if (sensor.getType() == Sensor.TYPE_ACCELEROMETER) {
            float x = event.values[0];
            float y = event.values[1];
            float z = event.values[2];
            long currentTime = System.currentTimeMillis();
            if ((currentTime - lastTime) > 100) {
                long diffTime = (currentTime - lastTime);
                lastTime = currentTime;
                float speed = Math.abs(x + y + z - lastX - lastY - lastZ)/ diffTime * 10000;
                if (speed > THRESHOLD) {
                    getRandomNumber();
                }
                lastX = x;
                lastY = y;
                lastZ = z;
            }
        }
    }

    @Override
    public void onAccuracyChanged(Sensor sensor, int accuracy) {

    }
```

当应用程序休眠以节省电池电量时，最好注销传感器。再次在`MainActivity`类中添加这些函数:

```
protected void onPause() {
        super.onPause();
        mSensorManager.unregisterListener(this);
    }

    protected void onResume() {
        super.onResume();
        mSensorManager.registerListener(this, mAccelerometer, SensorManager.SENSOR_DELAY_NORMAL);
    }

    private void getRandomNumber() {
        Random randNumber = new Random();
        int iNumber = randNumber.nextInt(100);
        TextView text = (TextView)findViewById(R.id.number);
        text.setText("" + iNumber);
        RelativeLayout ball = (RelativeLayout) findViewById(R.id.ball);
        Animation a = AnimationUtils.loadAnimation(this, R.anim.move_down_ball_first);
        ball.setVisibility(View.INVISIBLE);
        ball.setVisibility(View.VISIBLE);
        ball.clearAnimation();
        ball.startAnimation(a);
    }

    public class UpdateLocationClick implements View.OnClickListener {

        @Override
        public void onClick(View v) {
            if (SApplication.LOCATION != null) {
                double lat = SApplication.LOCATION.getLatitude();
                double lon = SApplication.LOCATION.getLongitude();
                coordinates.setText(lat + " " + lon);
                Geocoder geocoder = new Geocoder(getApplicationContext(), new Locale("en"));
                try {
                    // get address from location
                    List<Address> addresses = geocoder.getFromLocation(lat, lon, 1);
                    if (addresses != null && addresses.size() != 0) {
                        StringBuilder builder = new StringBuilder();
                        Address returnAddress = addresses.get(0);
                        for (int i = 0; i < returnAddress.getMaxAddressLineIndex(); i++) {
                            builder.append(returnAddress.getAddressLine(i));
                            builder.append(" ");
                        }
                        address.setText(builder);
                        address.setVisibility(View.VISIBLE);
                    } else {
                        Log.e(TAG, "Addresses null");
                    }
                } catch (IOException e) {
                    Log.e(TAG, "Geocoder exception " + e);
                }
            } else {
                Toast.makeText(getApplicationContext(), "Check GPS status and internet connection", Toast.LENGTH_LONG).show();
                coordinates.setText("No location yet");
                address.setVisibility(View.INVISIBLE);
            }
        }

    }
```

Android 传感器值允许我们检测用户在主平面表面的实时 GPS 位置。只要有 Wi-Fi 连接或 3G/4G 网络，这些数据就会发送给有权访问监控应用程序的人。

与加速度计不同，独立的 GPS 单元依赖于卫星无线电指示器。在移动监控中，AGPS(辅助全球定位系统)优于 GPS。这是因为这些网络塔配备了 GPS 接收器来提高信号速度。接收器接收来自卫星的信息，然后通过所有必要的计算处理将信息发送到手机。因此，您会收到:

*   快速位置获取
*   更少的电池使用
*   室内位置采集

首先，我们向`AndroidManifest.xml`文件添加一些权限:

```
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.INTERNET" />
```

我们将使用后台服务来检测新位置。创建一个名为`LocationService.java`的新类文件，并向其中添加以下内容:

```
public class LocationService extends Service {
    private static final String TAG = LocationService.class.getSimpleName();
    private LocationManager mLocationManager = null;
    private static final int INTERVAL = 1000; // minimum time interval between location updates (milliseconds)
    private static final float DISTANCE = 10f; // minimum distance between location updates (meters)

    private class LocationListener implements android.location.LocationListener {
        Location mLastLocation;

        public LocationListener(String provider) {
            Log.e(TAG, "LocationListener " + provider);
            mLastLocation = new Location(provider);
        }

        @Override
        public void onLocationChanged(Location location) {
            mLastLocation.set(location);
            SApplication.LOCATION = location;
        }

        @Override
        public void onProviderDisabled(String provider) {
        }

        @Override
        public void onProviderEnabled(String provider) {
        }

        @Override
        public void onStatusChanged(String provider, int status, Bundle extras) {
        }

    }

    LocationListener[] mLocationListeners = new LocationListener[]{
            new LocationListener(LocationManager.GPS_PROVIDER),
            new LocationListener(LocationManager.NETWORK_PROVIDER)
    };

    @Override
    public IBinder onBind(Intent arg0) {
        return null;
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        super.onStartCommand(intent, flags, startId);
        return START_STICKY;
    }
}
```

现在，我们向 GPS 和网络提供商请求位置更新。将以下内容添加到`LocationService`类中:

```
@Override
    public void onCreate()
    {
        if (mLocationManager == null) {
            mLocationManager = (LocationManager) getApplicationContext().getSystemService(Context.LOCATION_SERVICE);
        }try {
        mLocationManager.requestLocationUpdates(LocationManager.NETWORK_PROVIDER, INTERVAL, DISTANCE, mLocationListeners[1]);
    } catch (java.lang.SecurityException ex) {
        Log.i(TAG, "fail to request location update, ignore", ex);
    } catch (IllegalArgumentException ex) {
        Log.d(TAG, "network provider does not exist, " + ex.getMessage());
    }
        try {
            mLocationManager.requestLocationUpdates(LocationManager.GPS_PROVIDER, INTERVAL, DISTANCE, mLocationListeners[0]);
        } catch (java.lang.SecurityException ex) {
            Log.i(TAG, "fail to request location update, ignore", ex);
        } catch (IllegalArgumentException ex) {
            Log.d(TAG, "gps provider does not exist " + ex.getMessage());
        }
    }

    @Override
    public void onDestroy()
    {
        super.onDestroy();
        if (mLocationManager != null) {
            for (int i = 0; i < mLocationListeners.length; i++) {
                try {
                    mLocationManager.removeUpdates(mLocationListeners[i]);
                } catch (Exception ex) {
                    Log.i(TAG, "fail to remove location listeners, ignore", ex);
                }
            }
        }
    }
```

现在，我们定义我们的应用程序类，创建一个名为`SApplication`的新类，并向其添加以下内容:

```
public class SApplication extends Application {
	private final String TAG = SApplication.class.getSimpleName();
 	public static Location LOCATION = null;</p>

	public void onCreate() {
    	Intent location = new Intent(getApplicationContext(), LocationService.class);
    	startService(location);
	}
}
```

要完成这个应用程序，需要很多用户界面元素，查看 GitHub 上的库[来查看它们，并将它们导入到你自己的项目中。为了快速实现这一点，您可能会发现在演示项目中用“res”文件夹替换“res”文件夹中的一些内容会更容易。](https://github.com/sitepoint-editors/SensorProject)

要测试这个应用程序，你需要在真实的设备上运行它，而不是在模拟器上。摇动手机以查看加速度计值，并触摸
更新按钮以获取您的位置。如果您无法让位置工作，请尝试启用“高精度”模式。

这是一个简单的例子来说明 Android 的一些传感器的潜力，希望你能看到在现实世界中的潜在用途。

## 分享这篇文章