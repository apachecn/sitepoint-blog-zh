# 用嘲弄来嘲弄你的测试依赖

> 原文：<https://www.sitepoint.com/mock-test-dependencies-mockery/>

虽然不是每个人都这样做，但是测试应用程序是开发人员最重要的部分之一。单元测试是最常运行的测试。通过单元测试，你可以检查一个类的行为是否和你预期的完全一样。有时，您在应用程序中使用第三方服务，很难设置好一切来测试这个单元。这正是嘲讽发挥作用的时候。

### 什么是嘲讽？

模仿一个对象无非就是创建一个替身对象，在单元测试中替代真实的对象。如果您的应用程序严重依赖依赖注入，那么模仿是一条可行之路。

模仿对象有几个原因

1.  当执行单元测试时，最好隔离这个类。您不希望另一个类或服务干扰您的单元测试。
2.  该对象尚不存在。您可以首先创建测试，然后构建最终的对象。
3.  模拟对象通常比准备整个数据库进行测试要快。

当运行单元测试时，您可能正在使用 PHPUnit。PHPUnit 带有一些默认的模仿能力，正如你在[文档](http://phpunit.de/manual/current/en/test-doubles.html#test-doubles.mock-objects)中看到的。你可以在 Jeune Asuncion 写的[这篇文章](https://www.sitepoint.com/an-introduction-to-mock-object-testing)中阅读更多关于嘲弄和 PHPUnit 的嘲弄能力。

在本文中，我们将深入了解由 Pádraic Brady 创建的库[mocking](https://github.com/padraic/mockery)。我们将创建一个温度类，注入一个当前不存在的天气服务。

### 设置

让我们从建立我们的项目开始。我们从一个包含以下内容的`composer.json`文件开始。这将确保我们有嘲弄和 PHPUnit 可用。

```
{
    "name": "sitepoint/weather",
    "license": "MIT",
    "type": "project",
    "require": {
        "php": ">=5.3.3"
    },
    "autoload": {
        "psr-0": { "": "src/" }
    },
    "require-dev": {
        "phpunit/phpunit": "4.1.*",
        "mockery/mockery": "0.9.*"
    }
}
```

我们还创建了一个名为`phpunit.xml`的 PHPUnit 配置文件

```
<phpunit>
    <testsuite name="SitePoint Weather">
        <directory>src</directory>
    </testsuite>
    <listeners>
        <listener class="\Mockery\Adapter\Phpunit\TestListener"
                  file="vendor/mockery/mockery/library/Mockery/Adapter/Phpunit/TestListener.php">
        </listener>
    </listeners>
</phpunit>
```

定义这个监听器很重要。如果没有监听器，像`once()`、`twice()`和`times()`这样的方法如果使用不当就不会抛出错误。稍后会详细介绍。

我还创建了两个目录。保存我的类的目录`src`和保存我们的测试的目录`tests`。在`src`目录中，我创建了路径`SitePoint\Weather`。

我们从创建`WeatherServiceInterface`开始。我们不存在的天气服务将实现这个接口。在这种情况下，我们只提供一种方法，它将给出以摄氏度为单位的温度。

```
namespace SitePoint\Weather;

interface WeatherServiceInterface
{
    /**
     * Return the Celsius temperature
     *
     * @return float
     */
    public function getTempCelsius();
}
```

所以，我们有一项服务，提供摄氏温度。我想知道华氏温度。为此，我创建了一个名为`TemperatureService`的新类。该服务将注入天气服务。接下来，我们还定义了一个将摄氏温度转换为华氏温度的方法。

```
namespace SitePoint\Weather;

class TemperatureService
{
    /**
     * @var WeatherServiceInterace $weatherService Holds the weather service
     */
    private $weatherService;

    /**
     * Constructor.
     *
     * @param WeatherServiceInterface $weatherService
     */
    public function __construct(WeatherServiceInterface $weatherService) {
        $this->weatherService = $weatherService;
    }

    /**
     * Get current temperature in Fahrenheit
     *
     * @return float
     */
    public function getTempFahrenheit() {
        return ($this->weatherService->getTempCelsius() * 1.8000) + 32;
    }
}
```

### 创建单元测试

我们已经准备好了设置单元测试的一切。我们在`tests`目录中创建一个`TemperatureServiceTest`类。在这个类中，我们创建了方法`testGetTempFahrenheit()`，它将测试我们的华氏方法。

在这个方法中要做的第一步是创建一个新的`TemperatureService`对象。就在我们这么做的时候，我们的构造函数将请求一个实现了`WeatherServiceInterface`的对象。由于我们还没有这样的对象(我们也不想要)，我们将使用 mock 为我们创建一个模拟对象。让我们看看这个方法完全完成后会是什么样子。

```
namespace SitePoint\Weather\Tests;

use SitePoint\Weather\TemperatureService;

class TemperatureServiceTest extends \PHPUnit_Framework_TestCase 
{

    public function testGetTempFahrenheit() {
        $weatherServiceMock = \Mockery::mock('SitePoint\Weather\WeatherServiceInterface');
        $weatherServiceMock->shouldReceive('getTempCelsius')->once()->andReturn(25);

        $temperatureService = new TemperatureService($weatherServiceMock);
        $this->assertEquals(77, $temperatureService->getTempFahrenheit());
    }

}
```

我们从创建模拟对象开始。我们告诉 mock 我们想要模仿哪个对象(或接口)。第二步是描述将在这个模拟对象上调用哪个方法。在`shouldReceive()`方法中，我们定义了将被调用的方法的名称。

我们定义这个方法将被调用多少次。我们可以用`once()`、`twice()`和`times(X)`。在这种情况下，我们希望它只被调用一次。如果不调用或者调用次数太多，单元测试就会失败。

最后，我们在`andReturn()`方法中定义将要返回的值。在这种情况下，我们返回`25`。嘲讽也有`andReturnNull()`、`andReturnSelf()`、`andReturnUndefined()`这样的返回方法。如果这是你所期望的，嘲弄也能够抛出异常。

我们现在有了模拟对象，可以像往常一样创建我们的`TemperatureService`对象并进行测试。25 摄氏度是 77 华氏度，所以我们检查是否从我们的`getTempFahrenheit()`方法接收到 77 华氏度。

如果在根目录下运行`vendor/bin/phpunit tests/`, PHPUnit 会给你开绿灯，表示一切正常。

### 先进的

上面的例子相当简单。没有参数，只有一个简单的调用。让我们把事情变得复杂一点。

比方说，我们的气象服务也有一种方法来获得准确时间的温度。我们将下面的方法添加到当前的`WeatherServiceInterface`中。

```
/**
 * Return the Celsius temperature by hour
 * 
 * @param $hour
 * @return float
 */
public function getTempByHour($hour);
```

我们想知道，晚上 0 点到 6 点之间的平均温度是多少。为此，我们在`TemperatureService`中创建了一个计算平均温度的新方法。为此，我们从我们的`WeatherService`中检索 7 个温度并计算平均值。

```
/**
 * Get average temperature of the night
 * 
 * @return float
 */
public function getAvgNightTemp() {
    $nightHours = array(0, 1, 2, 3, 4, 5, 6);
    $totalTemperature = 0;

    foreach($nightHours as $hour) {
        $totalTemperature += $this->weatherService->getTempByHour($hour);
    }

    return $totalTemperature / count($nightHours);
}
```

让我们来看看我们的测试方法。

```
public function testGetAvgNightTemp() {
    $weatherServiceMock = \Mockery::mock('SitePoint\Weather\WeatherServiceInterface');
    $weatherServiceMock->shouldReceive('getTempByHour')
        ->times(7)
        ->with(\Mockery::anyOf(0, 1, 2, 3, 4, 5, 6))
        ->andReturn(14, 13, 12, 11, 12, 12, 13);

    $temperatureService = new TemperatureService($weatherServiceMock);
    $this->assertEquals(12.43, $temperatureService->getAvgNightTemp());
}
```

我们再次模拟接口，并定义将被调用的方法。接下来，我们定义这个方法将被调用多少次。我们在前面的例子中使用了`once()`，现在我们使用`times(7)`来表示我们期望这个方法被调用 7 次。如果没有准确地调用该方法 7 次，测试将会失败。如果您没有在`phpunit.xml`配置文件中定义监听器，您就不会收到这方面的通知。

接下来，我们定义`with()`方法。在 with 方法中，可以定义所需的参数。在这种情况下，我们期待 7 个不同的小时。

最后，我们有`andReturn()`方法。在本例中，我们指出了返回的 7 个值。如果您定义的返回值较少，则每次都会重复最后一个可用的返回值。

当然，嘲弄可以做得更多。要获得完整的指南和文档，我建议你看一看 Github 页面。

如果你对上面那个项目的代码感兴趣，可以看看[这个 Github 页面](https://github.com/peternijssen/sitepoint-weather)。

## 结论

使用 PHPUnit，您已经可以模仿对象了。然而，你也可以像上面的例子解释的那样使用嘲弄。如果你正在对你的类进行单元测试，并且你不希望任何其他类影响你的测试，mockery 可以很容易地帮你解决这个问题。如果你真的想做功能测试，最好看看你是否能整合真实的交易。你目前在使用 PHPUnit 嘲讽吗，并且正在考虑换成嘲讽？你想在后续文章中看到更多更大的嘲讽例子吗？请在下面的评论中告诉我。

## 分享这篇文章