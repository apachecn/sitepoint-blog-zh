# 使用 JSONP API 加载 Flickr 图片

> 原文：<https://www.sitepoint.com/load-flickr-photos-using-jsonapi/>

**本文更新于 2016 年 6 月 23 日。具体来说:修改了代码示例，添加了 CodePen 演示，并修复了格式问题。**

Flickr 是雅虎旗下领先的图片和视频托管网站。他们提供了一个公共 API，可以用来返回符合特定标准的照片列表。

要返回 JSON 格式的 API 响应，您可以在请求中发送一个值为`json`的参数`format`。例如，下面将返回标记为“小猫”的照片列表。

```
https://api.flickr.com/services/feeds/photos_public.gne?tags=kitten&format=json 
```

这种查询 Flickr API 的方法不需要身份验证。你可以在这里找到可能的参数列表:[https://www . Flickr . com/services/API/Flickr . photos . search . html](https://www.flickr.com/services/api/flickr.photos.search.html)。

## 典型的 Flickr JSON 对象

这是典型的 Flickr JSON 对象的样子:

```
jsonFlickrFeed({
  "title": "Recent Uploads tagged kitten",
  "link": "http://www.flickr.com/photos/tags/kitten/",
  "description": "",
  "modified": "2016-06-19T09:32:56Z",
  "generator": "http://www.flickr.com/",
  "items": [
    {
      "title": "Portrait of Simba and Mogli",
      "link": "http://www.flickr.com/photos/112684907@N06/27665373772/",
      "media": {"m":"http://farm8.staticflickr.com/7442/27665373772_25bb8acec1_m.jpg"},
      "date_taken": "2016-06-17T17:09:38-08:00",
      "description": " <p><a href=\"http://www.flickr.com/people/112684907@N06/\">stefanhuber92<\/a> posted a photo:<\/p> <p><a href=\"http://www.flickr.com/photos/112684907@N06/27665373772/\" title=\"Portrait of Simba and Mogli\"><img src=\"http://farm8.staticflickr.com/7442/27665373772_25bb8acec1_m.jpg\" width=\"177\" height=\"240\" alt=\"Portrait of Simba and Mogli\" /><\/a><\/p> ",
      "published": "2016-06-19T09:32:56Z",
      "author": "nobody@flickr.com (stefanhuber92)",
      "author_id": "112684907@N06",
      "tags": "pet cats cute eye animal animals cat tiere kitten siblings katze katzen fell haustier liebe tier gemütlich petlove geschwister kuscheln schön catlove süs petlover"
    },
    ...
    19 more items
    ...
  ]
}) 
```

## 使用 JSONP API 加载 Flickr 图片

如您所见，JSON 响应被包装在函数`jsonFlickrFeed`中。我们可以用它从 Flickr 中提取图片，并在我们的网页上显示出来，如下所示:

```
function jsonFlickrFeed(json) {
  $.each(json.items, function(i, item) {
    $("<img />").attr("src", item.media.m).appendTo("#images");
  });
};

$.ajax({
  url: 'https://api.flickr.com/services/feeds/photos_public.gne',
  dataType: 'jsonp',
  data: { "tags": "kitten", "format": "json" }
}); 
```

如果你想了解更多关于 JSONP 的知识，请阅读: [jQuery 的 JSONP 示例说明](https://www.sitepoint.com/jsonp-examples/)

## 演示

这就是我们的结局。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [vKXZrz](http://codepen.io/SitePoint/pen/vKXZrz/) 。