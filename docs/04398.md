# 用 Flickr API 创建图片库——风格和逻辑

> 原文：<https://www.sitepoint.com/creating-image-gallery-flickr-api-style-logic/>

在这个关于如何使用 Flickr API 开发一个简单图库的迷你系列的第一部分中，我们讨论了项目的需求、构建 HTML 页面所需的标记以及五个 CSS 模块中的两个。

在第二部分，也是最后一部分，我们将介绍剩余的 CSS 模块和支持这个项目的 JavaScript 代码。事不宜迟，我们开始吧。

## 样式(续)

在前一篇文章中，我们讨论了助手类的模块和布局模块。列表中的下一个是图库模块。

### 画廊模块

图库模块定义了图库及其组件的样式。它由简单的声明组成，我将强调几个有趣的地方。

第一点是类为`gallery`的元素，它作为以自然大小显示的照片的容器，被赋予 500px 的固定高度。然后，其内部用于显示所选图像的`img`元素通过将其`max-height`和`max-width`属性设置为`100%`而受到约束。通过这样做，我们确保图像不会溢出容器。

第二点是我们定义了当用户悬停或聚焦在箭头上时，箭头的样式如何变化。焦点事件的样式很重要，因为它增强了用户通过键盘(例如，通过点击`TAB`键)导航网站时元素的可访问性。

如果你认为自己是 CSS 的初学者，你可能还想研究一下按钮是如何做成圆形的，以及箭头是如何绘制的。

该模块的完整代码如下所示:

```
.gallery
{
   position: relative;
   height: 500px;
   border: 1px solid #FFFFFF;
}

.gallery img
{
   display: block;
   margin: 0 auto;
   max-width: 100%;
   max-height: 100%;
}

.gallery__arrow
{
   position: absolute;
   top: 50%;
   display: block;
   width: 60px;
   height: 60px;
   border: none;
   border-radius: 50%;
   background-color: #000000;
   opacity: 0.7;
   cursor: pointer;
}

.gallery__arrow:hover,
.gallery__arrow:focus
{
   opacity: 1;
}

.gallery__arrow:before,
.gallery__arrow:after
{
   content: '';
   position: absolute;
   width: 10px;
   height: 40%;
   background-color: #FFFFFF;
}

.gallery__arrow:before
{
   bottom: 12px;
}

.gallery__arrow:after
{
   top: 12px;
}

.gallery__arrow:hover:before,
.gallery__arrow:focus:before,
.gallery__arrow:hover:after,
.gallery__arrow:focus:after
{
   background-color: #FCB712;
}

.gallery__arrow--left
{
   left: 0.5em;
}

.gallery__arrow--left:before
{
   transform: rotate(-40deg);
   left: 35%;
}

.gallery__arrow--left:after
{
   transform: rotate(40deg);
   left: 35%;
}

.gallery__arrow--right
{
   right: 0.5em;
}

.gallery__arrow--right:before
{
   transform: rotate(40deg);
   right: 35%;
}

.gallery__arrow--right:after
{
   transform: rotate(-40deg);
   right: 35%;
}
```

### 缩略图模块

缩略图模块不包含任何太花哨的东西。它通过将`width`属性设置为`19%`，将`margin-right`设置为`1%`，将`display`属性设置为`inline-block`，强制缩略图为五个一行。值得一提的另一点是，当缩略图悬停或聚焦时会产生一种效果，以增强可访问性，如前一节所述。

该模块的完整代码如下:

```
.thumbnails__list,
.thumbnails__pager
{
   margin: 0;
   padding: 0;
   list-style-type: none;
}

.thumbnails__list li
{
   display: inline-block;
   width: 19%;
   margin-top: 1%;
   margin-right: 1%;
}

.thumbnail
{
   width: 100%;
}

.thumbnail:hover,
.thumbnail:focus
{
   border: 1px solid #FCB720;
   opacity: 0.7;
}

.thumbnails__pager
{
   text-align: right;
   margin: 0.5em 0;
}

.thumbnails__pager li
{
   display: inline;
}

.thumbnails__pager a
{
   margin: 0 0.2em;
   color: #FFFFFF;
   text-decoration: none;
}

.thumbnails__pager a.current,
.thumbnails__pager a:hover,
.thumbnails__pager a:focus
{
   color: #FCB720;
   text-decoration: underline;
}
```

### 主页模块

最后一个模块是主页模块。这里是我们设计项目元素的地方，这些元素不适合任何其他模块，并且是特定于主页的。在处理实际项目时，您经常会发现自己设计的元素只在给定的页面上具有特定的外观，在这种情况下，为该页面创建特定的 CSS 文件是有意义的。

homepage.css 文件的完整代码如下所示:

```
.form-search
{
   margin: 0.5em 0;
   text-align: right;
}

.form-search #query
{
   padding: 0.2em;
}

.form-search input
{
   color: #000000;
}

.thumbnails
{
   border-bottom: 3px solid #FFFFFF;
}

.copyright
{
   margin-top: 0.5em;
   margin-bottom: 0.5em;
   text-align: right;
}
```

在最后一个模块中，我们已经完成了对用于设计项目样式的 CSS 文件的概述，所以现在是讨论业务逻辑的时候了。

## 商业逻辑

项目的业务逻辑也被组织成小模块，其中一个文件“main.js”充当标记和 JavaScript 模块之间的粘合剂。在这个文件中，我们将定义图片库按钮的事件处理程序，当用户单击页面中的一个链接时会发生什么，以及当用户搜索一些给定的文本时该做什么。

在我们检查每个模块的特性之前，我想强调一下我使用的一些有趣的技术。首先，每个模块都是用一个[life(立即调用的函数表达式)](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)定义的，允许我们创建*私有*变量和方法，避免污染全局范围。第二，在每个模块中，我都采用了[严格模式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)，它对 JavaScript 代码的执行方式实施了更严格的规则。例如，它<q>通过将一些 JavaScript 静默错误改为抛出错误</q>来消除它们。最后，每个文件实现了[模块模式](http://addyosmani.com/resources/essentialjsdesignpatterns/book/#modulepatternjavascript)。

记住这几点，让我们看看定义的模块。

### 实用模块

我们将讨论的第一个模块是实用程序模块。它包含了大家都感兴趣的方法，我们的 JavaScript 模块将会用到这些方法。它只定义了两个方法:`extend`和`buildUrl`。

`extend`方法是 jQuery 中同名方法的简化版本，用于将两个或更多对象的属性合并为一个(第一个参数)。如果您不是 JavaScript 爱好者，您可能想了解我是如何通过使用 [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) 让这个方法接受任意数量的对象的。`arguments`是一个类似数组的对象，对应于传递给函数的参数。

`buildUrl`方法用于创建包含查询字符串的有效 URL，从一个 URL 和一个在查询字符串中使用的名称和值的对象开始。

实用模块的代码定义如下:

```
(function(document, window) {
   'use strict';

   function buildUrl(url, parameters){
      var queryString = '';

      for(var key in parameters) {
         if (parameters.hasOwnProperty(key)) {
            queryString += encodeURIComponent(key) + '=' + encodeURIComponent(parameters[key]) + '&';
         }
      }

      if (queryString.lastIndexOf('&') === queryString.length - 1){
         queryString = queryString.substring(0, queryString.length - 1);
      }

      return url + '?' + queryString;
   }

   function extend(object) {
      for(var i = 1; i < arguments.length; i++) {
          for(var key in arguments[i]) {
             if (arguments[i].hasOwnProperty(key)) {
                object[key] = arguments[i][key];
             }
          }
      }

      return object;
   }

   window.Utility = {
      buildUrl: buildUrl,
      extend: extend
   };
})(document, window);
```

### 画廊模块

gallery 模块定义了一个暴露在全局范围内的`Gallery`对象。它的构造函数接受两个参数:属于图库的照片列表(即包含照片 URL 的数组)，以及以自然大小显示图像的 DOM 元素。这个对象定义了我们图库的特性，比如显示上一个(`showPrevious`方法)或下一个(`showNext`方法)图像的能力，或者创建缩略图列表(`createThumbnailsGallery`方法)。

这个模块演示了一个有趣的技术，用于解决在处理循环和事件处理程序时出现的常见闭包问题。我已经在我的文章 [5 更多 JavaScript 面试练习](https://www.sitepoint.com/5-javascript-interview-exercises/)中讨论了这个问题及其解决方案(第 1 点和第 2 点)。这里定义在循环外的函数是`clickHandler()`。

现在，您已经了解了本模块中使用的技巧，可以开始阅读完整的源代码了:

```
(function(document, window) {
   'use strict';

   function Gallery(photos, container) {
      this.currentIndex = 0;
      this.photos = photos;
      this.container = container;

      this.showPhoto(this.currentIndex);
   }

   Gallery.prototype.showPhoto = function(index) {
      if (index >= 0 && index < this.photos.length) {
         this.currentIndex = index;
         this.container.src = Flickr.buildPhotoLargeUrl(this.photos[this.currentIndex]);
      }
   };

   Gallery.prototype.showPrevious = function() {
      if (this.currentIndex > 0) {
         this.currentIndex--;
      }

      this.showPhoto(this.currentIndex);
   };

   Gallery.prototype.showNext = function() {
      if (this.currentIndex < this.photos.length - 1) {
         this.currentIndex++;
      }

      this.showPhoto(this.currentIndex);
   };

   Gallery.prototype.createThumbnailsGallery = function(container) {
      function clickHandler(index, gallery) {
         return function (event) {
            event.preventDefault();

            gallery.showPhoto(index);
         };
      }

      container.textContent = '';
      var image, link, listItem;
      for (var i = 0; i < this.photos.length; i++) {
         image = document.createElement('img');
         image.src = Flickr.buildThumbnailUrl(this.photos[i]);
         image.className = 'thumbnail';
         image.alt = this.photos[i].title;
         image.title = this.photos[i].title;

         link = document.createElement('a');
         link.href = image.src;
         link.addEventListener('click', clickHandler(i, this));
         link.appendChild(image);

         listItem = document.createElement('li');
         listItem.appendChild(link);

         container.appendChild(listItem);
      }
   };

   window.Gallery = Gallery;
})(document, window);
```

### Flickr 模块

在某种意义上，Flickr 模块是我们应用程序的核心，因为它定义了使用 Flickr API 的代码。与我们到目前为止讨论的其他模块不同，您可能希望扩展这个模块以提供更多的特性。例如，您可以扩展它，根据用户的用户名或照片的位置来搜索照片。出于这个原因，我将使用`Utility.extend()`方法，而不是仅仅在全局范围内公开`Flickr`对象，如下所示:

```
window.Flickr = Utility.extend(window.Flickr || {}, {
   /* methods of this module defined here */
});
```

在这个模块的另一部分中使用了`Utility.extend()`方法，特别是在`searchText()`方法的第一条语句中。在这种情况下，它用于将`searchText()`方法的调用者传递的参数与调用者不应该知道的模块的私有信息(因此是私有的)合并，例如要调用的 API 方法(`flickr.photos.search`)。

这个模块需要一个 API 密钥来与 Flickr API 通信。我不能与世界共享我的 API 密匙，所以你需要插入你自己的作为变量`apiKey`的值来拥有一个完全工作的项目。如果您不提供这样的密钥，您对 Flickr 的所有请求都将失败。

记住这最后一点，下面是本模块的完整代码:

```
(function(document, window) {
   'use strict';

   var apiKey = 'YOUR-API-KEY-HERE';
   var apiURL = 'https://api.flickr.com/services/rest/';

   function searchText(parameters) {
      var requestParameters = Utility.extend(parameters, {
         method: 'flickr.photos.search',
         api_key: apiKey,
         format: 'json'
      });

      var script = document.createElement('script');
      script.src = Utility.buildUrl(apiURL, requestParameters);
      document.head.appendChild(script);
      document.head.removeChild(script);
   }

   function buildThumbnailUrl(photo) {
      return 'https://farm' + photo.farm + '.staticflickr.com/' + photo.server +
      '/' + photo.id + '_' + photo.secret + '_q.jpg';
   }

   function buildPhotoUrl(photo) {
      return 'https://farm' + photo.farm + '.staticflickr.com/' + photo.server +
             '/' + photo.id + '_' + photo.secret + '.jpg';
   }

   function buildPhotoLargeUrl(photo) {
      return 'https://farm' + photo.farm + '.staticflickr.com/' + photo.server +
      '/' + photo.id + '_' + photo.secret + '_b.jpg';
   }

   window.Flickr = Utility.extend(window.Flickr || {}, {
      buildThumbnailUrl: buildThumbnailUrl,
      buildPhotoUrl: buildPhotoUrl,
      buildPhotoLargeUrl: buildPhotoLargeUrl,
      searchText: searchText
   });
})(document, window);
```

### 将所有这些联系在一起:主模块

现在，我们已经讨论了项目的所有模块，我们需要将它们与页面的 HTML 元素联系起来，以便当单击右箭头时，服务将显示列表中的下一张照片。这是 main.js 文件中包含的代码的作用。我想讨论代码的两个部分:页面和箭头。

寻呼机最多显示六页，外加特殊的“按钮”(实际上它们都是`a`元素),可以转到第一页和最后一页，以及转到上一页和下一页。当单击页面的一个元素时，服务必须显示属于该页面的缩略图。例如，如果用户点击第 3 页(记住，每页包含 15 个缩略图)，该服务应该向用户显示属于该页的照片，从第 31 <sup>张</sup>到第 45 <sup>张</sup>张找到的照片(如果有的话)。为了执行这个动作，我们*可以*在寻呼机的每个链接和特殊按钮上添加一个监听器，但是这是对内存的浪费。通过使用一种叫做*事件委派*的技术，我们可以更有效地完成这项工作。因此，我们将只向页面本身添加一个侦听器，而不是为页面的每个子页面添加一个侦听器。然后，根据触发 click 事件的元素，我们将执行预期的操作。(如果你不熟悉这个话题，你可以阅读大卫·沃尔什的文章【JavaScript 事件委派如何工作。)

我想提到的第二点是，我没有仅为`click`事件在两个箭头上添加一个事件监听器，而是也为`keydown`事件添加了一个监听器。通过这样做，我可以确定当焦点在箭头上时，用户是否按下了键盘上的某个键。然后，如果按下的键是`ENTER`键，我会执行与点击事件被触发时用户所期望的相同的动作。这种简单的方法使我们能够为那些通过键盘浏览网站的用户提高服务的可访问性。

这两个有趣的部分都可以在名为`init()`的函数中找到，下面显示了主模块的完整代码:

```
(function(document, window) {
   'use strict';

   var gallery;
   var lastSearch = 'London';

   function searchPhotos(text, page) {
      if (text.length === 0) {
         alert('Error: the field is required');
      }
      page = page > 0 ? page : 1;

      Flickr.searchText({
         text: text,
         per_page: 15,
         jsoncallback: 'Website.Homepage.showPhotos',
         page: page
      });
   }

   function createPager(element, parameters) {
      var pagesToShow = 5;
      var url = '/search/' + parameters.query + '/';
      element.textContent = '';

      var previousLinks = {
         '<<': 1,
         '<': (parameters.currentPage - 1 || parameters.currentPage)
      };

      for (var key in previousLinks) {
         link = document.createElement('a');
         link.href = url + previousLinks[key];
         link.innerHTML = '<span class="js-page-number visually-hidden">' + previousLinks[key] + '</span>' + key;
         var listItem = document.createElement('li');
         listItem.appendChild(link);
         element.appendChild(listItem);
      }

      // Avoid showing less than 6 pages in the pager because the user reaches the end
      var pagesDifference = parameters.pagesNumber - parameters.currentPage;
      var startIndex = parameters.currentPage;
      if (pagesDifference < pagesToShow) {
         startIndex = parameters.currentPage - (pagesToShow - pagesDifference - 1) || 1;
      }
      var link;
      for(var i = startIndex; i < parameters.currentPage + pagesToShow && i <= parameters.pagesNumber; i++) {
         link = document.createElement('a');
         link.href = url + i;
         link.innerHTML = '<span class="js-page-number">' + i + '</span>';
         if (i === parameters.currentPage) {
            link.className += ' current';
         }
         listItem = document.createElement('li');
         listItem.appendChild(link);
         element.appendChild(listItem);
      }

      var nextLinks = {
         '>': (parameters.currentPage === parameters.pagesNumber ? parameters.pagesNumber : parameters.currentPage + 1),
         '>>': parameters.pagesNumber
      };

      for (key in nextLinks) {
         link = document.createElement('a');
         link.href = url + nextLinks[key];
         link.innerHTML = '<span class="js-page-number visually-hidden">' + nextLinks[key] + '</span>' + key;
         var listItem = document.createElement('li');
         listItem.appendChild(link);
         element.appendChild(listItem);
      }
   }

   function showPhotos(data) {
      createPager(
         document.getElementsByClassName('js-thumbnails__pager')[0], {
            query: lastSearch,
            currentPage: data.photos.page,
            pagesNumber: data.photos.pages
         }
      );

      gallery = new Gallery(data.photos.photo, document.getElementsByClassName('js-gallery__image')[0]);
      gallery.createThumbnailsGallery(document.getElementsByClassName('js-thumbnails__list')[0]);
   }

   function init() {
      document.getElementsByClassName('js-form-search')[0].addEventListener('submit', function(event) {
         event.preventDefault();

         lastSearch = document.getElementById('query').value;
         if (lastSearch.length > 0) {
            searchPhotos(lastSearch, 1);
         }
      });

      var leftArrow = document.getElementsByClassName('js-gallery__arrow--left')[0];
      leftArrow.addEventListener('click', function() {
         gallery.showPrevious.bind(gallery)();
      });
      leftArrow.addEventListener('keydown', function(event) {
         if (event.which === 13) {
            gallery.showPrevious.bind(gallery)();
         }
      });

      var rightArrow = document.getElementsByClassName('js-gallery__arrow--right')[0];
      rightArrow.addEventListener('click', function() {
         gallery.showNext.bind(gallery)();
      });
      rightArrow.addEventListener('keydown', function(event) {
         if (event.which === 13) {
            gallery.showNext.bind(gallery)()();
         }
      });

      document.getElementsByClassName('js-thumbnails__pager')[0].addEventListener('click', function(event) {
         event.preventDefault();
         var page;
         var currentLink = this.getElementsByClassName('current')[0];
         if (event.target.nodeName === 'SPAN') {
            page = event.target.textContent;
         } else if (event.target.nodeName === 'A') {
            page = event.target.getElementsByClassName('js-page-number')[0].textContent;
         }

         // Avoid reloading the same page
         if (page && page !== currentLink.getElementsByClassName('js-page-number')[0].textContent) {
            searchPhotos(lastSearch, page);
         }
      });

      // Kickstart the page
      searchPhotos(lastSearch, 1);
   }

   window.Website = Utility.extend(window.Website || {}, {
      Homepage: {
         init: init,
         showPhotos: showPhotos
      }
   });
})(document, window);

Website.Homepage.init();
```

有了这最后一个文件的代码，我们终于完成了我们的项目。

## 结论

在这篇由两部分组成的文章中，我指导您创建了一个利用外部 API 的简单服务。通过使用 Flickr API，我们允许用户通过搜索标题和描述来生成 Flickr 照片库。我希望你喜欢它，并且你已经学会了一些新的和有趣的技术或方法。

该项目的源代码可以在名为 [Flickr gallery demo](https://github.com/AurelioDeRosa/flickr-gallery-demo) 的存储库中的[我的 GitHub 账户](https://github.com/AurelioDeRosa)上获得。

## 分享这篇文章