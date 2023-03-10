# 网站个性化:网站不应该更智能吗？

> 原文：<https://www.sitepoint.com/website-personalization/>

[![Personalized plates gallery](img/bd97c0b7a811d6ffd7b5857a9390e176.png)](https://www.flickr.com/photos/malingering/97038512/)

图片:装病

众所周知，网络产业发展迅速。

我们从静态网站开始，它们看起来总是一样的，是为坐在台式电脑前的人设计的。然后我们转向了移动网站，这很快演变成了响应式网站，无论用户是坐在桌面前，在超市排队时使用智能手机，还是坐在沙发上浏览 iPad，都能提供出色的体验。

我相信下一代网站将需要根据*而不仅仅是他或她使用的设备/屏幕*为每个用户动态显示个性化内容。

用户的体验也可以由其他因素定义，如时间、地点、网站的使用和用户档案。这种方法可以帮助消费者和企业。

*   消费者将获得更相关的网络体验。
*   企业将会看到访客到实际客户的更高转化率。

让我们来看看三种不同的个性化场景，以及我们如何实现每一种场景:

## 场景 A–基于位置的个性化

我是一个餐馆老板，有两个位置；一个在旧金山，一个在洛杉矶。虽然我为两家餐厅都有一个网站，但每个地方都有不同的促销活动。

我想在主页上突出促销，但也想确保来自洛杉矶的用户将看到洛杉矶促销，来自旧金山的用户将看到旧金山特价。

这是我希望我的网站的样子:

#### 来自洛杉矶的游客:

![View #1: Hollywood](img/99e69cddb784380d93f1b8f843139b8c.png)

#### 来自旧金山的游客:

![View #2: San Francisco](img/b827cd3cb62d4264d1498c4753def576.png)

如何实施:

1.在你的 HTML 中实现不同的促销部分，给它们唯一的 id 并使用 CSS 隐藏它们。

```
<section id="promotion-la" style="display:none">
    <!-- your LA promotion html goes here -->
</section>
<section id="promotion-sf" style="display:none">
    <!-- your SF promotion html goes here -->
</section>
```

2.添加 JavaScript 代码，根据访问者的 IP 地址确定他们的位置，并相应地显示相关部分。在这个例子中，我们将使用 MaxMind JavaScript API([http://dev.maxmind.com/geoip/geoip2/javascript/](http://dev.maxmind.com/geoip/geoip2/javascript/))来获取位置数据。您也可以在服务器端代码中使用 MaxMind。

在这个例子中，我假设您使用的是 jQuery。

```
<script src="//js.maxmind.com/js/apis/geoip2/v2.1/geoip2.js" type="text/javascript"></script>
```

```
<script>
$(document).ready(function(){

    var onSuccess = function(location){
if(location.city && location.city.names && location.city.names.en)
        {
             if(location.city.names.en == "San Francisco")
            {
$("#promotion-sf").show();
            }
            else if(location.city.names.en == "Los Angeles")
            {
                 $("#promotion-la").show();
            }
        }
    );
    };
    var onError = function(error){
    alert(
      "Error:\n\n"
        + JSON.stringify(error, undefined, 4)
    );
    };

// get the user location
    geoip2.city(onSuccess, onError);
});
</script>
```

您还可以使用移动 JavaScript 位置 API([http://dev.w3.org/geo/api/spec-source.html](http://dev.w3.org/geo/api/spec-source.html))来构建基于用户确切位置(例如街道地址)的场景，但是这种方法有两个主要缺点:

1.  它只能在支持定位的设备上运行。
2.  它会向用户显示一个“*你允许这个网站使用你的当前位置*的警告，这可能会导致更高的跳出率。

## 场景 B–基于时间的个性化

假设我们想在菜单页面上显示当天的每日特色菜。

这是我想要的样子:

#### 周五的菜单页面:

![Friday special: Steak](img/441c12d73a8c421f2722991fd4c6f6ba.png)

#### 周六的菜单页面:

![Saturday Special: Shrimp](img/c7c97ead31d983dd0e2461e3a50e5da6.png)

周六特餐:虾

如何实施:

1.  在你的 HTML 中实现不同的每日特殊部分，给它们唯一的 id 并使用 CSS 隐藏它们。

```
<section  class=”Friday”  style="display:none">
    <!-- your Friday special html goes here -->
</section>
<section  class=”Saturday” style="display:none">
    <!-- your Saturday special html goes here -->
<section>
```

2.添加 JavaScript 代码来检查当天并显示相关的特殊部分。

```
<script>
$(document).ready(function(){

var d = new Date();
var weekday = new Array(7);
weekday[0]=  "Sunday";
weekday[1] = "Monday";
weekday[2] = "Tuesday";
weekday[3] = "Wednesday";
weekday[4] = "Thursday";
weekday[5] = "Friday";
weekday[6] = "Saturday";

var today = weekday[d.getDay()];
$('.'+today).show()});
</script>
```

您可以添加任何其他基于时间的方案。一天中特定时间的快乐时光消息、新年的特殊效果、早餐菜单与晚餐菜单等。

## 场景 C——基于访问的个性化

让我们再添加一个很酷的例子。

第一次访问我们网站的人通常会寻找与回访者不同的东西，回访者可能已经是我们餐厅的粉丝了。因此，让我们向访问过我们网站三次或三次以上的回头客展示加入我们邮件列表的提议，这样他们就可以在我们有特殊活动时得到通知。

这是我希望我的网站如何寻找访问过三次或更多次的访问者:

![Mailing list](img/4a58521cd54c06b3d281f3f36fe60cd0.png)

如何实施:

1.  在你的 HTML 中实现“加入我们的邮件列表”部分，给它一个唯一的 ID 并使用 CSS 隐藏它。

    ```
    <section id=”join”>
     your mailing list form goes here
    </section>

    ```

    1.  添加 JavaScript 代码，检查用户的访问次数，如果超过三次，就显示该部分。我们将使用 cookie 来存储用户的访问次数。

```
$(document).ready(function(){

function getTotalVisits()
{
    var  now = new Date(),
    // we will set a cookie to be valid for 1 year
    expireDays = 365, 
    // we will define a visit length as 30 minutes
            visitLength = 30 * 60000;

    // get the last pageview time.
    //We are using jQuery cookie plugin to get/set the cookies
    lastView = $.getCookie("last_view");
    if (lastView == null)
        lastView = now.getTime();
    // set the new last page view
    $.setCookie("last_view", now.getTime(), expireDays);

    // get the total visits from the cookie
    var numVisits = $.getCookie("total_visits");
    if(numVisits == null)
    {
        numVisits = 0;
    }
    // increment the total visits by 1
    numVisits = (numVisits* 1) + 1
    // set a new total visit if needed
    if (numVisits == 1 || now.getTime() - lastView &gt; visitLength) { // this is a new visit          
        $.setCookie("total_visits", numVisits, expireDays);             
    }
    return numVisits;
}

// show the join us section if total number of visits is bigger than 3
if(getTotalVisits() &gt;= 3)
{
    $("#join").show();
}
});
```

这只是三个例子；您可能会想到许多对您或您客户的业务有意义的其他用例。当你开始将多个触发器结合在一起时，它会变得非常有趣，并且特别有针对性。

想象一下在特定时间为来自纽约的用户做些什么，或者为使用移动设备的第一次访问者做些什么。

显然，您希望确保您正在实现的个性化正在实现您的目标，因此在这些场景中，当一个操作完成时，添加跟踪是一个好主意。你可以使用谷歌分析事件，Mixpanel 或任何其他分析软件。

在我看来，网站个性化最大的挑战是维护。如何在不使代码不可读的情况下添加那些用例。如何记住一个促销活动何时结束并删除代码？如何理解如果配置了多个场景，网站对每个用户来说会是什么样子？这些都是你在给你的网站添加大量的挂钩之前需要考虑的事情。

## 分享这篇文章