# Sublime 2 的 jQuery 代码片段的完整列表

> 原文：<https://www.sitepoint.com/full-list-jquery-snippets-sublime-2/>

当我学习 Sublime 2 的代码片段 shorthands 时，我发现拥有这个主要 jQuery 代码片段列表非常有用，所以我可以快速浏览并学习它们。因此，这里基本上是一个最好的 jQuery 代码片段的转储，它位于令人敬畏的新文本编辑器 Sublime 2 中。它们是按照相关性排列的(在我看来，最常用/最有用的排在最前面)。滚动快乐！


相关帖子:

*   [如何设置 Sublime 2 文本编辑器](http://www.jquery4u.com/editors/setup-sublime-2-text-editor)
*   [Sublime2 vs Notepad++](http://www.jquery4u.com/editors/sublime2-vs-notepad)
*   [使用 Sublime 2 文本编辑器简化 jQuery](http://www.jquery4u.com/editors/jquery-and-sublime-2-text-editor)
*   [在 Sublime 2 文本中映射新的 tab 命令](http://www.jquery4u.com/editors/mapping-commands-sublime-2)

**jQuery 文档就绪**
触发器:$。就绪(选项卡)

版本 1

```
$.jQuery(document).ready(function($) {
    // Stuff to do as soon as the DOM is ready. Use $() w/o colliding with other libs;
});
```

版本 2

```
$.jQuery(document).ready(function() {
    // Stuff to do as soon as the DOM is ready;
});
```

版本 3

```
$.$(document).ready(function() {
    // Stuff to do as soon as the DOM is ready;
});
```

**jQuery Ajax**
触发器:$。ajax (tab)

```
$.ajax({
  url: '/path/to/file',
  type: 'POST',
  dataType: 'xml/html/script/json/jsonp',
  data: {param1: 'value1'},
  complete: function(xhr, textStatus) {
    //called when complete
  },
  success: function(data, textStatus, xhr) {
    //called when successful
  },
  error: function(xhr, textStatus, errorThrown) {
    //called when there is an error
  }
});
```

**jQuery 每个**
触发器:$。每个(选项卡)

```
$.each(function(index) {
  this.innerHTML = this + " is the element, " + index + " is the position";
});

$.each(array/object, function(index, val) {
  //iterate through array or object
});
```

**jQuery getJSON**
触发器:$。getJSON (tab)

```
$.getJSON('/path/to/file', {param1: 'value1'}, function(json, textStatus) {
  //optional stuff to do after success
});
```

**jQuery getScript**
触发器:$。getScript 选项卡

```
$.getScript('path/to/file', function(data, textStatus) {
  //optional stuff to do after getScript
});
```

**脚本包括**
触发脚本(tab)

 `**jQuery Animate**
触发器:$。动画(选项卡)

版本 1

```
$.animate({param1: value1, param2: value2}, speed, function() {
  // stuff to do after animation is complete
})
```

版本 2

```
$.animate({param1: value1, param2: value2}, speed)
```

**jQuery DOM Attr 属性**
触发器:。属性(标签)

版本 1

```
.attr({
  attribute1: 'value1',
  attribute2: 'value2'
})
```

版本 2

```
.attr('attribute', 'value')
```

**jQuery Map**
触发器:$。地图(选项卡)

版本 1

```
$.map(function(index, elem) {
  return something;
})
```

版本 2

```
$.map(array, function(item, index) {
  return something;
});
```

**jQuery CSS**
触发器:。css(选项卡)

版本 1

```
.css({
  property1: 'value1',
  property2: 'value2'
})
```

版本 2

```
.css('property', 'value')
```

**jQuery 绑定**
触发器:。绑定(制表符)

```
.bind('event name', eventData, function(event) {
  // Act on the event
});
```

**jQuery Live**
触发器:。实时(选项卡)

```
.live('event type(s)', function(event) {
    // Act on the event
});
```

**jQuery 插件**
触发器:插件(tab)

版本 1

```
jQuery.fn.myeffect = function(speed, easing, callback) {
  return this.animate({param1: 'value'}, speed, easing, callback);
};
```

版本 2

```
(function($) {
  $.extend($.expr[':'], {
    selectorName: function(element, index, matches, set) {

      return something;
    }
  });
})(jQuery);
```

第三版<——太棒了！

```
(function($) {
// What does the pluginName plugin do?
$.fn.pluginName = function(options) {

  if (!this.length) { return this; }

  var opts = $.extend(true, {}, $.fn.pluginName.defaults, options);

  this.each(function() {
    var $this = $(this);

  });

  return this;
};

// default options
$.fn.pluginName.defaults = {
  defaultOne: true,
  defaultTwo: false,
  defaultThree: 'yay!'
};
})(jQuery);
```

**键值对**
触发器:(tab)

```
key: "value",
```

**jQuery Wrap**
触发器:。换行(制表符)

版本 1

```
.wrap('`')`

版本 2

```
.wrapAll('`')`

版本 3

```
.wrapInner('`')`

 **jQuery Toggle** 
触发器:。切换(选项卡)

版本 1

```
.toggle('slow/400/fast')
```

版本 2

```
.toggle(function() {
    // Stuff to do every *odd* time the element is clicked;
}, function() {
    // Stuff to do every *even* time the element is clicked;
});
```

 **jQuery 触发器** 
触发器:。触发器(选项卡)

版本 1

```
.trigger('event name')
```

版本 2

```
.triggerHandler('event name')
```

这些是主要的，但还有 30-40 个片段没有在这里提到，可以在 GitHub 上的 jQuery 片段完整列表中找到:[https://GitHub . com/kswedberg/jQuery-TM bundle/tree/master/Snippets](https://github.com/kswedberg/jquery-tmbundle/tree/master/Snippets)。

**我觉得有用的其他片段:**

 **HTML** 
触发器:html (tab)

```
 <title>

</code></pre>
<p><strong>HTML 4 Doctype Transitional</strong><br/>触发器:html:4t (tab)</p>
<pre class="language-"><code class="language-">< !DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">

    <meta http-equiv="Content-Type" content="text/html;charset=UTF-8"/>
    <title/>

</code></pre>
<p><strong>XHTML 1 Doctype Transitional</strong><br/>触发器:html:xt (tab)</p>
<pre class="language-"><code class="language-">< !DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <meta http-equiv="Content-Type" content="text/html;charset=UTF-8"/>
    <title/>

</code></pre>
</div><aside class="flex space-x-4"><h2 class="text-lg my-2 mr-2">分享这篇文章</h2><button aria-label="Share Sitepoint on facebook" data-network="facebook" class="social-share-button mr-2 cursor-pointer transform transition duration-200 hover:scale-125 text-primary-700"><svg aria-hidden="true" focusable="false" data-prefix="fab" data-icon="facebook-square" class="svg-inline--fa fa-facebook-square fa-w-14 fa-lg share-icon" role="img" viewbox="0 0 448 512"><path fill="currentColor" d="M400 32H48A48 48 0 0 0 0 80v352a48 48 0 0 0 48 48h137.25V327.69h-63V256h63v-54.64c0-62.15 37-96.48 93.67-96.48 27.14 0 55.52 4.84 55.52 4.84v61h-31.27c-30.81 0-40.42 19.12-40.42 38.73V256h68.78l-11 71.69h-57.78V480H400a48 48 0 0 0 48-48V80a48 48 0 0 0-48-48z"/></svg></button><button aria-label="Share Sitepoint on reddit" data-network="reddit" class="social-share-button mr-2 cursor-pointer transform transition duration-200 hover:scale-125 text-primary-700"><svg aria-hidden="true" focusable="false" data-prefix="fab" data-icon="reddit" class="svg-inline--fa fa-reddit fa-w-16 fa-lg share-icon" role="img" viewbox="0 0 512 512"><path fill="currentColor" d="M201.5 305.5c-13.8 0-24.9-11.1-24.9-24.6 0-13.8 11.1-24.9 24.9-24.9 13.6 0 24.6 11.1 24.6 24.9 0 13.6-11.1 24.6-24.6 24.6zM504 256c0 137-111 248-248 248S8 393 8 256 119 8 256 8s248 111 248 248zm-132.3-41.2c-9.4 0-17.7 3.9-23.8 10-22.4-15.5-52.6-25.5-86.1-26.6l17.4-78.3 55.4 12.5c0 13.6 11.1 24.6 24.6 24.6 13.8 0 24.9-11.3 24.9-24.9s-11.1-24.9-24.9-24.9c-9.7 0-18 5.8-22.1 13.8l-61.2-13.6c-3-.8-6.1 1.4-6.9 4.4l-19.1 86.4c-33.2 1.4-63.1 11.3-85.5 26.8-6.1-6.4-14.7-10.2-24.1-10.2-34.9 0-46.3 46.9-14.4 62.8-1.1 5-1.7 10.2-1.7 15.5 0 52.6 59.2 95.2 132 95.2 73.1 0 132.3-42.6 132.3-95.2 0-5.3-.6-10.8-1.9-15.8 31.3-16 19.8-62.5-14.9-62.5zM302.8 331c-18.2 18.2-76.1 17.9-93.6 0-2.2-2.2-6.1-2.2-8.3 0-2.5 2.5-2.5 6.4 0 8.6 22.8 22.8 87.3 22.8 110.2 0 2.5-2.2 2.5-6.1 0-8.6-2.2-2.2-6.1-2.2-8.3 0zm7.7-75c-13.6 0-24.6 11.1-24.6 24.9 0 13.6 11.1 24.6 24.6 24.6 13.8 0 24.9-11.1 24.9-24.6 0-13.8-11-24.9-24.9-24.9z"/></svg></button><button aria-label="Share Sitepoint on twitter" data-network="twitter" class="social-share-button mr-2 cursor-pointer transform transition duration-200 hover:scale-125 text-primary-700"><svg aria-hidden="true" focusable="false" data-prefix="fab" data-icon="twitter-square" class="svg-inline--fa fa-twitter-square fa-w-14 fa-lg share-icon" role="img" viewbox="0 0 448 512"><path fill="currentColor" d="M400 32H48C21.5 32 0 53.5 0 80v352c0 26.5 21.5 48 48 48h352c26.5 0 48-21.5 48-48V80c0-26.5-21.5-48-48-48zm-48.9 158.8c.2 2.8.2 5.7.2 8.5 0 86.7-66 186.6-186.6 186.6-37.2 0-71.7-10.8-100.7-29.4 5.3.6 10.4.8 15.8.8 30.7 0 58.9-10.4 81.4-28-28.8-.6-53-19.5-61.3-45.5 10.1 1.5 19.2 1.5 29.6-1.2-30-6.1-52.5-32.5-52.5-64.4v-.8c8.7 4.9 18.9 7.9 29.6 8.3a65.447 65.447 0 0 1-29.2-54.6c0-12.2 3.2-23.4 8.9-33.1 32.3 39.8 80.8 65.8 135.2 68.6-9.3-44.5 24-80.6 64-80.6 18.9 0 35.9 7.9 47.9 20.7 14.8-2.8 29-8.3 41.6-15.8-4.9 15.2-15.2 28-28.8 36.1 13.2-1.4 26-5.1 37.8-10.2-8.9 13.1-20.1 24.7-32.9 34z"/></svg></button><button aria-label="Share Sitepoint on linkedin" data-network="linkedin" class="social-share-button mr-2 cursor-pointer transform transition duration-200 hover:scale-125 text-primary-700"><svg aria-hidden="true" focusable="false" data-prefix="fab" data-icon="linkedin" class="svg-inline--fa fa-linkedin fa-w-14 fa-lg share-icon" role="img" viewbox="0 0 448 512"><path fill="currentColor" d="M416 32H31.9C14.3 32 0 46.5 0 64.3v383.4C0 465.5 14.3 480 31.9 480H416c17.6 0 32-14.5 32-32.3V64.3c0-17.8-14.4-32.3-32-32.3zM135.4 416H69V202.2h66.5V416zm-33.2-243c-21.3 0-38.5-17.3-38.5-38.5S80.9 96 102.2 96c21.2 0 38.5 17.3 38.5 38.5 0 21.3-17.2 38.5-38.5 38.5zm282.1 243h-66.4V312c0-24.8-.5-56.7-34.5-56.7-34.6 0-39.9 27-39.9 54.9V416h-66.4V202.2h63.7v29.2h.9c8.9-16.8 30.6-34.5 62.9-34.5 67.2 0 79.7 44.3 79.7 101.9V416z"/></svg></button><button aria-label="Share Sitepoint on email" data-network="email" class="social-share-button mr-2 cursor-pointer transform transition duration-200 hover:scale-125 text-primary-700"><svg aria-hidden="true" focusable="false" data-prefix="fas" data-icon="envelope" class="svg-inline--fa fa-envelope fa-w-16 fa-lg share-icon" role="img" viewbox="0 0 512 512"><path fill="currentColor" d="M502.3 190.8c3.9-3.1 9.7-.2 9.7 4.7V400c0 26.5-21.5 48-48 48H48c-26.5 0-48-21.5-48-48V195.6c0-5 5.7-7.8 9.7-4.7 22.4 17.4 52.1 39.5 154.1 113.6 21.1 15.4 56.7 47.8 92.2 47.6 35.7.3 72-32.8 92.3-47.6 102-74.1 131.6-96.3 154-113.7zM256 320c23.2.4 56.6-29.2 73.4-41.4 132.7-96.3 142.8-104.7 173.4-128.7 5.8-4.5 9.2-11.5 9.2-18.9v-19c0-26.5-21.5-48-48-48H48C21.5 64 0 85.5 0 112v19c0 7.4 3.4 14.3 9.2 18.9 30.6 23.9 40.7 32.4 173.4 128.7 16.8 12.2 50.2 41.8 73.4 41.4z"/></svg></button></aside>    
</body>
</html></title>
```

