# 显示谷歌分析 API 的流量统计

> 原文：<https://www.sitepoint.com/google-analytics-api/>

这就是你如何使用 **Google Analytics API 为你的网站获取流量统计数据**。我最初的目标是获得实时统计数据，并在博客上显示它们…但据我所知，谷歌分析 API 还不支持实时统计数据…但如果你想在你的网站上显示浏览量或访问量，并让它自动更新，这仍然是有用的。

## 在这篇文章中

*   设置您的谷歌分析 API 访问
*   前端 JavaScript 调用后端脚本并显示数据
*   连接到谷歌分析 API 的后端 PHP 脚本
*   实时工作演示
*   下载软件包供您亲自尝试

![blog-stats-graph](img/ce2921e2bfd37ab079efc3d7c88d18ab.png "blog-stats-graph")

![mini-graph](img/e62c33fd44cfba8dd2916f7d87d2d257.png "mini-graph")

## 设置您的谷歌分析 API 访问

这相当简单，应该只需要 2 分钟。去 https://code.google.com/apis/console/。

为您的项目激活分析 api。
![](img/47fd8ab7aac149e58a6e495e88621063.png "28-11-2012 5-30-32 PM")

创建一个新的 oAuth 2.0 客户端(单击蓝色大按钮…)
![](img/ffc8368896a60d37a4e81031c5268436.png "28-11-2012 5-32-00 PM")

确保 Google Analytics 服务已打开。
![](img/fc38875c952b4e0d7dbc78295d0e44a1.png "28-11-2012 5-32-31 PM")

你的屏幕应该看起来像这样:
![](img/3c64b60e302df65c04939a94e0cf9352.png "28-11-2012 5-33-39 PM")

## 前端脚本

这个脚本基本上向后端脚本发出一个 ajax 请求，然后解析 JSON 数据来创建图表。

```
/*
    Author: Sam Deering 2012
    Copyright: jquery4u.com
    Licence: MIT.
*/
(function($)
{

    $(document).ready(function()
    {

        //loading image for ajax request
        var $loading = $('#galoading');
        $.ajaxSetup(
        {
            start: function()
            {
                $loading.show();
            },
            complete: function()
            {
                $loading.hide();
            }
        });

        $.getJSON('data.php', function(data)
        {
            // console.log(data);
            if (data)
            {
                $('#results').show();

                //raw data
                $('#gadata').html(JSON.stringify(data));

                //mini graph
                var miniGraphData = new Array();
                $.each(data, function(i,v)
                {
                     miniGraphData.push([v["visits"], v["pageviews"]]);
                });
                // console.log(miniGraphData);
                $('#minigraph').sparkline(miniGraphData, { type: 'bar' });

                //get graph data
                var xAxisLabels = new Array(),
                    dPageviews = new Array(),
                    dVisits = new Array();
                $.each(data, function(i,v)
                {
                     xAxisLabels.push(v["date_day"]+'/'+v["date_month"]+'/'+v["date_year"]);
                     dPageviews.push(parseInt(v["pageviews"]));
                     dVisits.push(parseInt(v["visits"]));
                });
                console.log(xAxisLabels);
                console.log(dPageviews);
                console.log(dVisits);

                var chart = new Highcharts.Chart({
                    chart: {
                        renderTo: 'graph',
                        type: 'line',
                        marginRight: 130,
                        marginBottom: 25
                    },
                    title: {
                        text: 'jQuery4u Blog Traffic Stats',
                        x: -20 //center
                    },
                    subtitle: {
                        text: 'Source: Google Analytics API',
                        x: -20
                    },
                    xAxis: {
                        categories: xAxisLabels
                    },
                    yAxis: {
                        title: {
                            text: 'Pageviews'
                        },
                        plotLines: [{
                            value: 0,
                            width: 5,
                            color: '#FF4A0B'
                        }]
                    },
                    tooltip: {
                        formatter: function() {
                                return '**'+ this.series.name +'**
'+
                                this.x +': '+ this.y.toString().replace(/B(?=(d{3})+(?!d))/g, ",") +' pageviews';
                        }
                    },
                    legend: {
                        layout: 'vertical',
                        align: 'right',
                        verticalAlign: 'top',
                        x: -10,
                        y: 100,
                        borderWidth: 0
                    },
                    series: [
                    {
                        name: 'pageviews',
                        data: dPageviews
                    },
                    {
                        name: 'visits',
                        data: dVisits
                    }]
                });

            }
            else
            {
                $('#results').html('error?!?!').show();
            }
        });

    });

})(jQuery);
```

## 后端 PHP 脚本

这个脚本连接到 Google Analytics API，并获取与您的项目(网站)相关的数据。

```
setAccessType('online'); // default: offline
$client->setApplicationName('[INSERT YOUR APP NAME HERE]');
$client->setClientId('[INSERT YOUR KEY HERE]');
$client->setClientSecret('[INSERT YOUR KEY HERE]');
$client->setRedirectUri($scriptUri);
$client->setDeveloperKey('[INSERT YOUR KEY HERE]'); // API key

// $service implements the client interface, has to be set before auth call
$service = new Google_AnalyticsService($client);

if (isset($_GET['logout'])) { // logout: destroy token
    unset($_SESSION['token']);
    die('Logged out.');
}

if (isset($_GET['code'])) { // we received the positive auth callback, get the token and store it in session
    $client->authenticate();
    $_SESSION['token'] = $client->getAccessToken();
}

if (isset($_SESSION['token'])) { // extract token from session and configure client
    $token = $_SESSION['token'];
    $client->setAccessToken($token);
}

if (!$client->getAccessToken()) { // auth call to google
    $authUrl = $client->createAuthUrl();
    header("Location: ".$authUrl);
    die;
}

try {

    //specific project
    $projectId = '[INSERT PROJECT ID HERE]'; //see here for how to get your project id: http://goo.gl/Tpcgc

    // metrics
    $_params[] = 'date';
    $_params[] = 'date_year';
    $_params[] = 'date_month';
    $_params[] = 'date_day';
    // dimensions
    $_params[] = 'visits';
    $_params[] = 'pageviews';

    //for more metrics see here: http://goo.gl/Tpcgc

    // $from = date('Y-m-d', time()-2*24*60*60); // 2 days ago
    // $to = date('Y-m-d'); // today
    $from = date('Y-m-d', time()-7*24*60*60); // 7 days ago
    $to = date('Y-m-d', time()-24*60*60); // 1 days ago

    $metrics = 'ga:visits,ga:pageviews,ga:bounces,ga:entranceBounceRate,ga:visitBounceRate,ga:avgTimeOnSite';
    $dimensions = 'ga:date,ga:year,ga:month,ga:day';
    $data = $service->data_ga->get('ga:'.$projectId, $from, $to, $metrics, array('dimensions' => $dimensions));

    $retData = array();
    foreach($data['rows'] as $row)
    {
       $dataRow = array();
       foreach($_params as $colNr => $column)
       {
           $dataRow[$column] = $row[$colNr];
       }
       array_push($retData, $dataRow);
    }
    // var_dump($retData);
    echo json_encode($retData);

} catch (Exception $e) {
    die('An error occured: ' . $e->getMessage()."n");
}
?>
```

调试中的脚本:
![](img/56aa91be80401a7d5f77fd50a1bdda81.png "array-json")

## 演示

SitePoint 会不时删除存放在不同 HTML 页面上的多年前的演示。我们这样做是为了降低过时代码的风险，因为暴露的漏洞会给我们的用户带来风险。谢谢你的理解。

您需要获得许可才能观看演示。我很快会做一个公开演示。
![](img/8e08f21f3016de7f6cbb490254915b1a.png "premission")

## [计] 下载

[从 GitHub 下载源文件](https://github.com/sdeering/google-analytics-api)

## 分享这篇文章