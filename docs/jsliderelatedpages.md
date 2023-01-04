# jSlide 相关页面——jQuery 插件

> 原文：<https://www.sitepoint.com/jsliderelatedpages/>

大家好，我写了一个 jQuery 插件来显示相关的网页。当网站用户向下滚动页面时，相关网页会从屏幕的右下角滑出。当他们向上滚动页面时，它就消失了！；)牛逼。

[官方 jQuery 插件页面](https://plugins.jquery.com/project/jSlideRelatedPages)

**插件名称:** jsliderelatedpages
**作者:** jQuery4u
**供稿者:**萨姆·迪林
**版本:** 1.0
**发布日期:** 10/03/2011
**标签:** jSlide 相关，相关页面，相似页面，相关插件
**描述:**以流畅的方式显示相关网页

[下载包](https://www.sitepoint.com/wp-content/uploads/jquery4u/2011/03/jsliderelatedpages.zip)

## 使用

```
<script type="text/javascript">
  $().ready(function() {
       $('#jsrp_related').jsliderelatedposts();
</script>
```

或者您可以指定选项:

```
<script type="text/javascript">
  $().ready(function() {
	 $('#jsrp_related').jsliderelatedposts({
		 speed: 1000,
		 scrolltrigger: 0.75,
		 smartwidth: true
	 });
   });
</script>
```

## 选项:

*   **速度**–【1-5】–选择动画速度(默认:1 秒)5 秒是相当慢的滑动！
*   **滚动触发器**–[55-100%]–选择页面滚动的百分比(默认值:75%) 1.0(使其保持在底部，直到点击关闭按钮)。
*   **宽度**–[真/假]–使用智能宽度或全宽度(适合屏幕宽度)

## jQuery 代码

```
 // Anonymous function to wrap around your function to avoid conflict
(function($){

    //Attach this new method to jQuery
    $.fn.extend({

        //The function - pass the options variable to the function
        jsliderelatedposts: function(options) {

            //Set the default values, use comma to separate the settings
            var defaults = {
				 speed: 1000,
				 scrolltrigger: 0.75,
				 smartwidth: true
            }

            var options =  $.extend(defaults, options);

            return this.each(function() {

                var o = options;
				var animatesliderdiv = $(this);
				var jslide_width = animatesliderdiv.width();

				//IE fixes (div width issues)
				if ( $.browser.msie ) {

					if (o.smartwidth) {
						//smart width
						jslide_width = jslide_width * 0.58; //0.53 //0.422
					}
					else {
						//full width
						jslide_width = $(window).width();
					}

					animatesliderdiv.css({position: "absolute", width: jslide_width, right: "-"+jslide_width});
					animatesliderdiv.addClass('fixie');

				} else {

					if (!o.smartwidth) {

						//full width for browsers other than IE
						jslide_width = $(window).width();

					}

					animatesliderdiv.css({position: "fixed", width: jslide_width, right: "-"+jslide_width});

				}

				var webpage = $("body");
				var webpage_height = webpage.height();
				//show the window after % of the web page is scrolled.
				var trigger_height = webpage_height * o.scrolltrigger;

				//function to catch the user scroll
				var is_animating = false;
				var is_visible = false;

				$(window).scroll(function(){

					//dont stop the animation in action
					if (!is_animating) {

						if (!is_visible) {

							//reaching the bottom of page trigger
							if ($(window).scrollTop() > (webpage_height-trigger_height)) {

								showjsrp_related();

							}

						}else {

							//reaching top of page trigger
							if ($(window).scrollTop() 
```

## HTML 代码

```
<div id="jsrp_related">
		<!-- use custom close button if you wish -->
		<a id="jsrp_related-close" href="#"><img id="close_btn" src="close_button.gif" alt="Close" /></a>
<h3>Related Posts:</h3>
<ul>
	<li><a rel="bookmark" href="urltowebpage"><img class="jsrp_thumb" title="imagetitle" src="urltowebpageimage" border="0" alt="imagealt" width="50" height="50" /></a> <a class="jsrp_title" rel="bookmark" href="webpageurl">Web Page Title</a></li>
		<!-- page link 2 -->
		<!-- page link 3 etc -->
		<!-- add more pages in list format -->
</ul>
</div>
```

## CSS 代码

```
#jsrp_related {
	display:none; /*position:fixed;*/
	background-color:#A7BFF2; border:2px solid #779CED;
	bottom:0; right:0; /* left:0; */
	z-index: 10000;
	/*width:auto; set width of bar to width of entire window*/
	-moz-border-radius-topleft:10px;
	-webkit-border-top-left-radius:10px;
	border-top-left-radius:10px;
}
#jsrp_related h3 { margin:0; padding:5px 5px 5px 10px; font-size:20px; font-weight:bold;
		color:#F5F4F0; text-shadow: #6374AB 2px 2px 2px;
}
#jsrp_related ul { margin:0; padding:0; }
#jsrp_related ul li { float:left; padding:10px; background-color:white; list-style:none; vertical-align:middle; min-height:50px; border:thin solid #E6E6E6; }
#jsrp_related ul li:hover { background-color:#E6E6E6; }
#jsrp_related ul li a img { vertical-align:middle; }
#jsrp_related-close { float:right; }
#close_btn { border:0; text-decoration:none; }
.hidexscroll { overflow-x:hidden; }
.showxscroll { overflow-y:auto; }

/*IE FIX*/
.fixie {
  left: expression( ( - jsrp_related.offsetWidth + ( document.documentElement.clientWidth ? document.documentElement.clientWidth : document.body.clientWidth ) + ( ignoreMe2 = document.documentElement.scrollLeft ? document.documentElement.scrollLeft : document.body.scrollLeft ) ) + 'px' );
  top: expression( ( - jsrp_related.offsetHeight + ( document.documentElement.clientHeight ? document.documentElement.clientHeight : document.body.clientHeight ) + ( ignoreMe = document.documentElement.scrollTop ? document.documentElement.scrollTop : document.body.scrollTop ) ) + 'px' );
}
```

## 技术材料

IE7 或更低版本不喜欢 CSS 属性——position:fixed，所以我把它编码成使用 position:absolute。这意味着一些旧版本的 IE 将不会显示流畅的动画(它仍然正常工作，但会显示帖子！).

## 分享这篇文章