# 以随机顺序显示博客广告点

> 原文：<https://www.sitepoint.com/display-blog-ads-spots-random-order/>

为了让每个在博客上注册了 jQuery 广告的人公平起见，我写了一个漂亮的小函数**来随机加载广告**。还有一个随机播放按钮，作为在广告位上手动创建随机播放的演示。这一功能可以更加个性化，但确实有效。

自己试试吧(看右边栏！).

![jquery-blog-ads](img/478e7803399b530d8b574f108d5148c4.png "jquery-blog-ads")

## 代码

```
//Function to display ads on the jQuery Blog and shuffle
(function($)
{
   var displayAds = {

       ads: {
           1: {
                title: "jQuery UI Widgets for PC, Mobile and Touch devices",
                href: "http://www.jqwidgets.com",
                img: "jqwidgets.png"
           },
           2: {
                title: "jQuery Chop Slider 2.0 - The most eye catching image slider on the internet!",
                href: "http://www.idangero.us/cs/",
                img: "idangerous.jpg"
           },
           3: {
                title: "Sauce Labs - Online Cross Browser Testing",
                href: "http://www.saucelabs.com/scouthome?utm_source=banner&amp;utm_medium=flat&amp;utm_campaign=all+all+banner+jquery4u",
                img: "sauce-labs.jpg"
           },
           4: {
                title: "Diamond Slider - Ken Burns Effect &amp; Unlimited Transitions",
                href: "http://www.diamond.billykids-lab.net/",
                img: "diamond-slider.jpg"
           },
           5: {
                title: "AJAX Zoom - jQuery Dynamic 2d/360 Degrees Zoom with ipad support.",
                href: "http://www.ajax-zoom.com",
                img: "ajax-zoom.jpg"
           }
       },

       signupAd: {
           title: "Advertise here",
           href: "http://www.jquery4u.com/advertise/"
       },

       shuffleAds: function(arr)
       {
          for(var j, x, i = arr.length; i; j = parseInt(Math.random() * i), x = arr[--i], arr[i] = arr[j], arr[j] = x);
          return arr;
       },

       load: function()
       {
           $('#shuffle-ads').hide();
           var adContainer = $('#jq4u-sidebar-ads').empty(), adsArray = Array();

           /* construct ads */
           $.each(this.ads, function(i,v)
           {
                adsArray.push('<a rel="nofollow noopener" target="_blank" href="' + v.href + '"><div class="jq4uadbox"><img title="' + v.title + '" alt="' + v.title + '" src="https://www.jquery4u.cimg/ads/' + v.img + '"/></div></a>');
           });

           /* shuffle ads in random order */
           adsArray = this.shuffleAds(adsArray);

           /* output ads */
           $.each(adsArray, function(i,v)
           {
              adContainer.append(v);
           });

           /* add the signup ad */
           adContainer.append('<a href="' + this.signupAd.href + '"><div class="jq4uadvertisebox">' + this.signupAd.title + '</div></a>');

           /* show shuffle button */
           adContainer.append('<a href="#" id="shuffle-ads">Shuffle</a>');
           $('#shuffle-ads').live('click', function(e)
           {
              e.preventDefault();
              displayAds.load();

           });
           $('#shuffle-ads').show();
       }
   }

   $(document).ready(function() {
      displayAds.load();
   });

})(jQuery);
```

## 分享这篇文章