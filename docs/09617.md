# 创建一个很酷的 jQuery 社会化共享工具

> 原文：<https://www.sitepoint.com/jquery-socialize-sharing-tool/>

这是一个非常酷的社交网络共享工具，它有很好的 CSS3 效果，当你不想再看到它时，它会隐藏起来！它有一个非常酷的滑动效果，高度/宽度可以调整，你可以添加/删除你想看/不想看的图标。如果你选择这个解决方案，请先在 IE 中测试，你可能需要创建一个单独的 IE 样式表。虽然它在 Firefox、Chrome 和 Safari 上运行起来非常漂亮！尽情享受吧！:-)

为了让它工作，你必须包括 jQuery，jQuery Cookie 脚本来记住最小化状态，share 脚本和一点 CSS。它获取当前页面的标题，并提交给你选择的社交网站。

## JQUERY

```
(function( $ ) {
  $(document).ready(function() { 
    var url = window.location.href;
    var host =  window.location.hostname;
    var title = $('title').text();
    title = escape(title); //clean up unusual characters

    var twit = 'http://twitter.com/home?status='+title+'%20'+url;
    var facebook = 'http://www.facebook.com/sharer.php?u='+url
    var digg = 'http://digg.com/submit?phase=2&url='+url+'&amp;title='+title;
    var stumbleupon = 'http://stumbleupon.com/submit?url='+url+'&amp;title='+title;
    var buzz = 'http://www.google.com/reader/link?url='+url+'&amp;title='+title+'&amp;srcURL='+host;
    var delicious  = 'http://del.icio.us/post?url='+url+'&amp;title='+title;

    var tbar = '<div id="socializethis"><span>Share<br /><a href="#min" id="minimize" title="Minimize"> <img srcimg/minimize.png" /> </a></span><div id="sicons">';
    tbar += '<a href="'+twit+'" id="twit" title="Share on twitter"><img srcimg/twitter.png"  alt="Share on Twitter" width="32" height="32" /></a>';
    tbar += '<a href="'+facebook+'" id="facebook" title="Share on Facebook"><img srcimg/facebook.png"  alt="Share on facebook" width="32" height="32" /></a>';
    tbar += '<a href="'+digg+'" id="digg" title="Share on Digg"><img srcimg/digg.png"  alt="Share on Digg" width="32" height="32" /></a>';
    tbar += '<a href="'+stumbleupon+'" id="stumbleupon" title="Share on Stumbleupon"><img srcimg/stumbleupon.png"  alt="Share on Stumbleupon" width="32" height="32" /></a>';
    tbar += '<a href="'+delicious+'" id="delicious" title="Share on Del.icio.us"><img srcimg/delicious.png"  alt="Share on Delicious" width="32" height="32" /></a>';
    tbar += '<a href="'+buzz+'" id="buzz" title="Share on Buzz"><img srcimg/google-buzz.png"  alt="Share on Buzz" width="32" height="32" /></a>';
    tbar += '</div></div>';

    // Add the share tool bar.
    $('body').append(tbar); 
    $('#socializethis').css({opacity: .7}); 
    // hover.
    $('#socializethis').bind('mouseenter',function(){
      $(this).animate({height:'35px', width:'260px', opacity: 1}, 300);
      $('#socializethis img').css('display', 'inline');   
    });
    //leave
    $('#socializethis').bind('mouseleave',function(){
      $(this).animate({ opacity: .7}, 300);
    });  
    // Click minimize
    $('#socializethis #minimize').click( function() { 
      minshare(); 
      $.cookie('minshare', '1');  
    }); 

    if($.cookie('minshare') == 1){
      minshare();
    }  

    function minshare(){
      $('#socializethis').animate({height:'15px', width: '40px', opacity: .7}, 300); 
      $('#socializethis img').css('display', 'none');
      return false;
    }  
  });
})(jQuery);
```

## 超文本标记语言

```
<head>
<title>This will be sent to the social sites</title>
<script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.4.2/jquery.min.js"></script>
<script type="text/javascript" src="path/to/share.js"></script> 
<script type="text/javascript" src="path/to/jquery.cookie.js"></script> 
<link rel="stylesheet" href="path/to/style.css" type="text/css" />
</head>
```

## 半铸钢ˌ钢性铸铁(Cast Semi-Steel)

```
#socializethis{
  background:#CCC;
  border:solid 1px #666;
  border-width: 1px 0 0 1px;
  height:15px;
  width:260px;
  position:fixed;
  bottom:0;
  right:0;
  padding:2px 5px;
  overflow:hidden; 
  /* CSS3 */
  -webkit-border-top-left-radius: 12px;
  -moz-border-radius-topleft: 12px;
  border-top-left-radius: 12px;
  -moz-box-shadow: -3px -3px 3px rgba(0,0,0,0.5);
  -webkit-box-shadow: -3px -3px 3px rgba(0,0,0,0.5);
  box-shadow: -3px -3px 3px rgba(0,0,0,0.5);
  }

#socializethis a{
	float:left; 
  width:32px;
  margin:3px 2px 2px 2px; 
  padding:0; 
}

#socializethis span{ 
  float:left; 
  margin:2px 3px; 
  text-shadow: 1px 1px 1px #FFF;
  color:#444;
  font-size:12px;
}
```

## 分享这篇文章