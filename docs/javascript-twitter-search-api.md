# JavaScript Twitter 搜索 API 示例

> 原文：<https://www.sitepoint.com/javascript-twitter-search-api/>

今天，我想使用**公共 Twitter 搜索 API** 来抓取标记为“jquery4u”的最新 5 条推文。Twitter 提供了一堆有用的 [REST API 资源](https://dev.twitter.com/docs/api)，你可以在没有 Twitter 账户或任何身份验证(oAuth 或类似的)的情况下利用这些资源从 Twitter 获取数据。 [Twitter 搜索 API](https://dev.twitter.com/docs/using-search) 可用于使用查询抓取推文，例如具有特定关键词、标签、用户、提及、短语等的推文。以下是你如何做的。

## JavaScript/jQuery

在这里，我们可以从 Twitter 搜索 API 访问公共提要，并为数据指定一个 JSON 回调。

**参数:**
q =搜索字符串(确保它是 uri 编码的)
rpp =我们想要获取的 tweets 的数量

```
//get the JSON data from the Twitter search API
$.getJSON("http://search.twitter.com/search.json?q=jquery4u&rpp=5&callback=?", function(data)
{
    //loop the tweets
    $(data.results).each(function(i,v)
    {
        //...see full code below
    }
}
```

## HTML

```
<div id="twitter-widget"><!-- tweets go here... --></div>
```

## CSS

```
.twitStream{
    font-family: verdana;
    font-size: 11px;
}
.twitStream a{
    font-family: verdana;
    font-size: 11px;
}
.tweet{
    display: block;
    padding: .4em;
    margin: .4em 0;
}
.tweet-left{
    float: left;
    margin-right: 1em;
}
.tweet-left img{
    border: 2px solid #000000;
}
.tweet p.text{
    margin: 0;
    padding: 0;
}
```

## 完整代码列表

下面是 Twitter 小部件的完整 JavaScript 对象。

```
/**
 *  JQUERY4U
 *
 *  Displays the latest tweets.
 *
 *  @author      Sam Deering
 *  @copyright   Copyright (c) 2012 JQUERY4U Pty Ltd
 *  @license     http://jquery4u.com/license/
 *  @since       Version 1.0
 *  @filesource  js/jquery4u.twitter.js
 *
 */

(function($,W,D)
{
    W.JQUERY4U = W.JQUERY4U || {};
    W.JQUERY4U.TWITTER = {

        name: "JQUERY4U TWITTER",

        init: function(wid)
        {
            //helper functions
            String.prototype.linkify=function(){
              return this.replace(/[A-Za-z]+://[A-Za-z0-9-_]+.[A-Za-z0-9-_:%&;?/.=]+/g,function(m){
                return m.link(m);
              });
            };
            String.prototype.linkuser=function(){
              return this.replace(/[@]+[A-Za-z0-9-_]+/g,function(u){
                return u.link("http://twitter.com/"+u.replace("@",""));
              });
            };
            String.prototype.linktag=function(){
              return this.replace(/[]+[A-Za-z0-9-_]+/,function(t){
                return t;
              });
            };

            //load twitter stylesheet
            $("head").append('<link rel="stylesheet" type="text/css" href="css/style.css"/>');

            //get the tweets from Twitter API
            $.getJSON("http://search.twitter.com/search.json?q=jquery4u&rpp=5&callback=?", function(data)
            {
                // console.log(data.items.length);
                $(data.results).each(function(i,v)
                {

                      var tTime=new Date(Date.parse(this.created_at));
                      var cTime=new Date();
                      var sinceMin=Math.round((cTime-tTime)/60000);
                      if(sinceMin==0){
                        var sinceSec=Math.round((cTime-tTime)/1000);
                        if(sinceSec<10)
                          var since='less than 10 seconds ago';
                        else if(sinceSec<20)
                          var since='less than 20 seconds ago';
                        else
                          var since='half a minute ago';
                      }
                      else if(sinceMin==1){
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
                      else{
                        var sinceDay=Math.round(sinceMin/1440);
                        var since=sinceDay+' days ago';
                      }
                      var tweetBy='<a class="tweet-user" target="_blank" href="http://twitter.com/'+this.from_user+'">@'+this.from_user+'</a> <span class="tweet-time">'+since+'';
                      tweetBy=tweetBy+' &middot; <a class="tweet-reply" target="_blank" href="http://twitter.com/?status=@'+this.from_user+' &in_reply_to_status_id='+this.id+'&in_reply_to='+this.from_user+'">Reply</a>';
                      tweetBy=tweetBy+' &middot; <a class="tweet-view" target="_blank" href="http://twitter.com/'+this.from_user+'/statuses/'+this.id+'">View Tweet</a>';
                      tweetBy=tweetBy+' &middot; <a class="tweet-rt" target="_blank" href="http://twitter.com/?status=RT @'+this.from_user+' '+escape(this.text.replace(/&quot;/g,'"'))+'&in_reply_to_status_id='+this.id+'&in_reply_to='+this.from_user+'">RT</a>';
                      var tweet='<div class="tweet"><div class="tweet-left"><a target="_blank" href="http://twitter.com/'+this.from_user+'"><img width="48" height="48" alt="'+this.from_user+' on Twitter" src="'+this.profile_image_url+'" /></a></div><div class="tweet-right"><p class="text">'+this.text.linkify().linkuser().linktag().replace(/<a /g,'<a target="_blank"')+'<br></a>'+tweetBy+'</p></div><br style="clear: both;" /></div>';

                      $('#twitter').append(tweet); //add the tweet...

                });
            });
        }
    }

})(jQuery,window,document);
```

上面使用的一些代码是由 TwitStream 提供的。谢谢伙计们。

## 分享这篇文章