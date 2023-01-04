# 使用 JavaScript 计算自 Tweet 以来的 Twitter 时间

> 原文：<https://www.sitepoint.com/calculate-twitter-time-tweet-javascript/>

这个脚本计算了自从 tweet 创建以来的**“Twitter 时间”，这是从 Twitter REST API search.json 的一个名为“created_at”的属性中返回的(例如:“Tue，12 Jun 2012 06:24:59 +0000”)。这个函数可以用来在 Twittter 小部件或类似的部件上显示“自 tweet 以来的时间”,然后可以使用 setInterval 来刷新自 tweet 以来的时间(例如，见下面)。**

## calculateSince 函数()

```
/**
  * Calculates the Twitter time since the tweet was created
  * @param datetime returned by Twitter API in created_at
  * @return time since in html
  */
function calculateSince(datetime)
{
    var tTime=new Date(datetime);
    var cTime=new Date();
    var sinceMin=Math.round((cTime-tTime)/60000);
    if(sinceMin==0)
    {
        var sinceSec=Math.round((cTime-tTime)/1000);
        if(sinceSec<10)
          var since='less than 10 seconds ago';
        else if(sinceSec<20)
          var since='less than 20 seconds ago';
        else
          var since='half a minute ago';
    }
    else if(sinceMin==1)
    {
        var sinceSec=Math.round((cTime-tTime)/1000);
        if(sinceSec==30)
          var since='half a minute ago';
        else if(sinceSec<60)
          var since='less than a minute ago';
        else
          var since='1 minute ago';
    }
    else if(sinceMin<45)
        var since=sinceMin+' minutes ago';
    else if(sinceMin>44&&sinceMin<60)
        var since='about 1 hour ago';
    else if(sinceMin<1440){
        var sinceHr=Math.round(sinceMin/60);
    if(sinceHr==1)
      var since='about 1 hour ago';
    else
      var since='about '+sinceHr+' hours ago';
    }
    else if(sinceMin>1439&&sinceMin<2880)
        var since='1 day ago';
    else
    {
        var sinceDay=Math.round(sinceMin/1440);
        var since=sinceDay+' days ago';
    }
    return since;
};
```

## 刷新发推以来的时间

这就是如何使用 setInterval 来刷新使用上面的 calculateSince 函数发布 tweeted 以来的时间。

```
//auto refresh interval to load more tweets
setInterval(function()
{
    console.log('updating time since...');
    var tweets = $('#tweets .tweet');
    $.each(tweets, function(i,v)
    {
        //update the time since for the tweet
        $(v).find('.tweet-time').html(calculateSince($(v).find('.tweet-user').attr('created_at'))).fadeIn();
    });
}, 30000);
```

## 分享这篇文章