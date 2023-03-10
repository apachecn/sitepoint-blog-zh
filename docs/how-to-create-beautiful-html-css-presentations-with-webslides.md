# 如何用网络幻灯片创建漂亮的 HTML & CSS 演示文稿

> 原文：<https://www.sitepoint.com/how-to-create-beautiful-html-css-presentations-with-webslides/>

![HTML Presentations with WebSlides](img/499efdb6a9da19763bdfbf7342b7642a.png)

*这篇文章由[拉尔夫·梅森](https://github.com/pageaffairs)、[朱利奥·迈纳迪](https://www.sitepoint.com/author/gmainardi/)和[米哈伊尔·罗马诺夫](https://github.com/romanovma)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

演示是向观众提供信息的最佳方式之一。这种形式短小精悍，由小而易消化的片段组成，这使得任何讨论的话题都引人入胜，更容易理解。演示文稿可以包含各种数据，由许多不同的元素表示，如表格、图表、图解、插图、图像、视频、声音、地图、列表等，所有这些都为这种表达媒介提供了极大的灵活性。

特别是在网络上，演示文稿在很多场合都很方便，并且有大量的工具供您使用来创建一些漂亮的演示文稿。今天，我将向您介绍 [WebSlides](https://webslides.tv/) —这是一个小巧紧凑的库，有一套很好的现成组件，您可以利用它们来构建制作精良且有吸引力的 web 演示文稿:

> “网络幻灯片”是讲故事，并以一种美丽的方式分享它

事实上，WebSlides 的一个主要好处是，你可以用各种不同的方式漂亮地分享你的故事。使用同一个架构——40 多个具有语义类的组件，以及干净且可扩展的代码——您可以创建投资组合、登陆、长格式、采访等。

此外，您还可以通过与第三方服务和工具结合来扩展 WebSlides 的功能，如 [Unsplash](https://unsplash.com/) 、 [Animate.css](https://daneden.github.io/animate.css) 、 [Animate On Scroll](http://michalsnik.github.io/aos/) 等。

网络幻灯片简单易学，使用有趣。现在让我们来看看它的运行情况。

## 网络幻灯片入门

首先[下载网络幻灯片](https://webslides.tv/webslides-latest.zip)开始。然后，在根文件夹中，新建一个文件夹，命名为`presentation`。在新创建的`presentation`文件夹中，创建一个新文件并命名为`index.html`。现在，输入以下代码，其中包含对 WebSlides 文件的所需引用(确保文件路径对应于您的设置中的文件夹结构):

```
<!doctype html>
<html>
  <head>

    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css?family=Roboto:100,100i,300,300i,400,400i,700,700i%7CMaitree:200,300,400,600,700&subset=latin-ext" rel="stylesheet"/>
    <!-- CSS Base -->
    <link rel="stylesheet" type='text/css' media='all' href="../static/css/base.css"/>
    <!-- CSS Colors -->
    <link rel="stylesheet" type='text/css' media='all' href="../static/css/colors.css"/>
    <!-- Optional - CSS SVG Icons (Font Awesome) -->
    <link rel="stylesheet" type='text/css' media='all' href="../static/css/svg-icons.css"/>

  <body>
    <!-- PUT WEBSLIDES PRESENTATION CONTENT HERE -->

    <script src="../static/js/webslides.js"></script>
    <script> window.ws = new WebSlides(); </script>

    <!-- OPTIONAL - svg-icons.js (fontastic.me - Font Awesome as svg icons) -->
    <script defer src="../static/js/svg-icons.js"></script>
  </body>
```

现在，你已经准备好了。

## 使用网页快讯创建 web 演示文稿

在本节中，您将创建一个简短但完整的演示文稿，解释为什么 SVG 是 web 图形的未来。
注:如果你对 SVG 感兴趣，请查看我的文章: [SVG 101:什么是 SVG？](https://www.sitepoint.com/svg-101-what-is-svg/)和[如何在 Adobe Illustrator 中优化和导出 SVGs】。](https://www.sitepoint.com/crash-course-optimizing-and-exporting-svgs-in-adobe-illustrator/)

你将在每张幻灯片上一步一步地工作。让我们从第一个开始。

### 幻灯片 1

第一张幻灯片非常简单。它只包含一句话:

```
<article id="webslides">
  <!-- First slide -->
  <section class="bg-gradient-r aligncenter">
    <h1>Why SVG Is the Future of Web Graphics?</h1>
  </section>
  <!-- Second slide -->
  <section>
    ...
  </section>
  <!-- Third slide -->
  <section>
    ...
  </section>
  <!-- etc. -->
</article>
```

`<article id="webslides">`中的每个父级`<section>`创建一个单独的幻灯片。这里，你使用了 WebSlides 的两个类，即`bg-gradient-r`和`aligncenter`，分别应用径向渐变背景和将幻灯片内容居中对齐。

![WebSlides Presentation Demo: Slide 1](img/3ead00b5651e3a298262f17f4630e47d.png)

### 幻灯片 2

第二张幻灯片解释了什么是 SVG:

```
<section>
  <div class="wrap">
    <div class="content-left">
      <h2>What Is SVG?</h2>
      <p class="text-subtitle">Scalable Vector Graphics</p>
      <hr>
      <p class="text-intro">SVG is an XML-based vector graphic format used to display a wide range of graphics on the Web.</p>
      <p class="text-intro">SVG documents are just plain text files and can be created and edited in every text editor.</p>
    </div>
    <div class="content-right">
      <img src="traffic-lights.png">
    </div>
  </div>
</section>
```

上面的代码使用`content-left`和`content-right`类将内容分成两列。此外，为了让上面的类工作，您需要使用`wrap`类包装所有内容。在左侧，代码使用`text-subtitle`使文本全部大写，使用`text-intro`增加字体大小。右边是一幅插图。

![WebSlides Presentation Demo: Slide 2](img/5d84e6b8ef774362558706cd54a26f4f.png)

### 幻灯片 3

下一张幻灯片使用[网格组件](https://webslides.tv/demos/components#slide=9)创建两列:

```
<section class="aligncenter">
  <h2>SVG Is Future-Proof</h2>
  <p class="text-subtitle">As long as the W3C sets the global industry standards, it seems that SVG will continue to be the de-facto standard for vector graphics in the browser.</p>
  <hr>
  <div class="grid">
    <div class="column">
      <p class="text-intro" style="text-align: left">SVG is a W3C standard. This makes SVG future-proof. Technologies are rapidly changing. What's here today could be gone tomorrow. But SVG will most likely be around for a long time.</p>
    </div>
    <div class="column">
      <figure class="browser">
        <img src="standard.png">
      </figure>
    </div>
  </div>
</section>
```

上面的代码片段展示了如何使用`grid`和`column`类创建一个包含两列的网格。在第一列中，`style`属性将文本向左对齐(注意`<section>`元素上的`aligncenter`类是如何级联到它的`.column`子元素的，这会导致幻灯片中的所有文本居中对齐)。在第二列中，`browser`类使说明性的图像看起来像一个截屏。

![WebSlides Presentation Demo: Slide 3](img/c23b4c40bd6e13ff491aa67ce7b3290e.png)

### 幻灯片 4

在第四张幻灯片中，再次使用网格组件将内容分成两列:

```
<section>
  <h2 class="aligncenter">SVG Is the Best Graphic Format for the Web</h2>
  <hr>
  <div class="grid">
    <div class="column">
      <p class="text-intro">What makes SVG so preferable for the Web is its remarkable usefulness and unlimited flexibility. It's the only one graphic format which most closely and completely responds to the current web development demands such as <em>scalability, responsibility, interactivity, programmability, and accessibility</em>.</p>
    </div>
    <div class="column">
      <p class="text-intro">As a graphic format SVG perfectly suits the needs of the web because it satisfies both designers and developers. An illustration can be easily created by a designer with any vector software, and then, the exported SVG can be transferred to a developer who can add animation or some other interactivity.</p>
    </div>
  </div>
</section>
```

![WebSlides Presentation Demo: Slide 4](img/3b49690693d1cdf3cb2dcbf0377c5991.png)

### 幻灯片 5

在这张幻灯片中，分别使用`content-left`和`content-right`类将一半内容放在左边，另一半放在右边:

```
<section>
  <div class="wrap">
    <div class="content-left">
      <h2>SVG Usage Is Growing</h2>
      <p class="text-subtitle">Over the last year SVG usage has doubled.</p>
      <hr>
      <p>As of 2014 SVG usage is growing continuously (image below) and it has doubled over the last year (right image).</p>
      <img src="svg-usage-numbers.png">
    </div>
    <div class="content-right">
      <img src="svg-usage.png">
    </div>
  </div>
</section>
```

![WebSlides Presentation Demo: Slide 5](img/3726ff7ea0c2d178c77f1b32e6ec348c.png)

### 幻灯片 6

在这张幻灯片中，使用`background`类通过 [Unsplash 服务](https://unsplash.com)嵌入一个图像作为背景。使用`bg-trans-light`类将标题置于明亮透明的背景上。文本的颜色显示为白色，因为幻灯片使用了带有`bg-black`类的黑色背景，因此默认颜色是反色的，即黑底白字，而不是白底黑字。此外，为了使文本在图像前面可见，用`<div class="wrap">`将它包裹起来:

```
<section class="aligncenter bg-black">
  <span class="background" style="background-image:url('https://source.unsplash.com/ICW6QYOcdlg/800x600')"></span>
  <div class="wrap">
    <h2 class="bg-trans-light"><em>SVG Solves the Most Important Problems in Modern Web Development</em></h2>
  </div>
</section>
```

![WebSlides Presentation Demo: Slide 6](img/b8b386d2cde90b07d0cea4988f867ce5.png)

### 幻灯片 7

在这张幻灯片中，将解释文本放在左边，插图放在右边，大小为默认大小的 40%(在`<img>`元素上有`alignright`和`size-40`类)。对于此幻灯片和接下来的三张幻灯片，使用`slideInRight`，它是 WebSlides 的内置 CSS 动画之一:

```
<section class="slideInRight">
  <div class="wrap">
    <img src="responsive.jpg" class="alignright size-40">
    <h3>1\. Scalability</h3>
    <p class="text-subtitle">SVG is like an infinitely scalable canvas</p>
    <p>Because of its vector nature an SVG drawing can scale and adapt to any size without loosing sharpness. In contrast, raster-based formats, such as GIF, JPG, and PNG, are static and have fixed dimensions. Although techniques for responsive images are good, they still can not compete with the SVG's ability to scale infinitely. No matter what device you use, SVG will adapt and scale perfectly keeping the graphic's visual appearance intact.</p>
  </div>
</section>
```

![WebSlides Presentation Demo: Slide 7](img/dc233eb80c1a46f4fdc877f80e421f0e.png)

### 幻灯片 8

在这里做类似的事情:

```
<section  class="slideInRight">
  <div class="wrap">
    <img src="interactivity.jpg" class="alignright size-40">
    <h3>2\. Interactivity</h3>
    <p class="text-subtitle">SVG Drawings are dynamic and interactive</p>
    <p>SVG is fully scriptable. It can be manipulated with CSS and/or JavaScript. An SVG graphic can be animated and can respond to user actions.</p>
  </div>
</section>
```

![WebSlides Presentation Demo: Slide 8](img/498f3a6b8d8529aa94fb53378fa4ea01.png)

### 幻灯片 9

这张幻灯片也使用了类似的结构:

```
<section  class="slideInRight">
  <div class="wrap">
    <img src="svg-market-position.png" class="alignright size-40">
    <h3>3\. Performance</h3>
    <p class="text-subtitle">SVG is the preferred image format for high traffic sites</p>
    <p>Because SVG is just plain text, in most cases the file size will be smaller compared to its bitmap counterparts (JPEG and PNG). So the impact on web page performance will be minimized.</p>
  </div>
</section>
```

![WebSlides Presentation Demo: Slide 9](img/50f09fb86336a53cc8bba112a2274a82.png)

### 幻灯片 10

这里，再次将内容分为左右。在第二个`<p>`标签中，使用 inline `style`属性来调整`font-size`和`line-height`属性。这样做将覆盖默认情况下应用于元素的`text-intro`类样式。在右侧，使用`<div class="wrap size-80">`为 SVG 代码示例创建一个容器:

```
<section  class="slideInRight">
  <div class="wrap">
    <div class="content-left">
      <h3>4\. Accessibility</h3>
      <p class="text-subtitle">SVG text is real and editable</p>
      <p style="font-size: 1.8rem; line-height: 3.2rem">SVG files are text-based and can be searched and indexed. This makes them readable by screen readers, search engines and other devices. The ability of SVG to bring visuals to those who cannot see, extends its reach into many domains that pixel-based graphics just cannot go.</p>
    </div>
    <div class="content-right">
      <div class="wrap size-80">
        <pre>&lt;svg  viewBox="0 0 200 100"&gt;

&lt;text x="10" y="20"&gt;SVG offers real and editable text&lt;/text&gt;

&lt;/svg&gt;
        </pre>
      </div>
    </div>
  </div>
</section>
```

![WebSlides Presentation Demo: Slide 10](img/77c9e58e856487afd93a332ed4089f0c.png)

### 幻灯片 11

这里，利用您已经使用过的一些类来说明浏览器对 SVG 的支持:

```
<section class="aligncenter">
  <h2>SVG Has Very Good Browser Support</h2>
  <p class="text-subtitle">The browser support for SVG is 97% and above</p>
  <hr>
  <div class="wrap">
    <img src="caniuse.png">
  </div>
</section>
```

![WebSlides Presentation Demo: Slide 11](img/1b16bb34d88b15844dcb36ea8da842df.png)

### 幻灯片 12

在这张幻灯片中，以图片库的形式展示了 SVG 的一些使用案例。为此，对`flexblock`和`gallery`类使用一个无序列表。画廊中的每件物品都标有一个`li`标签:

```
<section>
  <h2>SVG Has Many Different Use Cases</h2>
  <hr>
  <ul class="flexblock gallery">
    <li>
     <a href="#">
       <figure>
         <img alt="Thumbnail" src="illustration.jpg">
         <figcaption>
           <h2>Plain Illustrations</h2>
         </figcaption>
       </figure>
     </a>
   </li>
   <li>
     <a href="#">
       <figure>
         <img alt="Thumbnail " src="icons.jpg">
         <figcaption>
           <h2>Logos and Icons</h2>
         </figcaption>
       </figure>
     </a>
   </li>
   <li>
     ...
   </li>
   ...
 </ul>
</section>
```

![WebSlides Presentation Demo: Slide 12](img/b6f74d4b314036761abfe9e7c78f2b6f.png)

### 幻灯片 13

本节展示了一个典型的 SVG 工作流，因此您需要使用`flexblock`和`steps`类，它们将内容显示为一系列步骤。同样，每一步都放在一个`li`标签中:

```
<section>
  <h2>The SVG Workflow Is Straightforward</h2>
  <ul class="flexblock steps">
    <li>
      <span>
        <svg class="fa-file-image-o">
          <use xlink:href="#fa-file-image-o"></use>
        </svg>
      </span>
      <h2>01\. Draw</h2>
      <p>Draw your graphic in a vector editing program such as Adobe Illustrator</p>
    </li>
    <li>
      <div class="process step-2"></div>
      <span>
        <svg class="fa-file-code-o">
          <use xlink:href="#fa-file-code-o"></use>
        </svg>
      </span>
      <h2>02\. Export</h2>
      <p>Export the drawing as an SVG file</p>
    </li>
    <li>
     ...
    </li>
  </ul>
</section>
```

对于第一步之后的每一步，都需要添加`process-step-#`类。这将添加一个指向下一步的三角形。

![WebSlides Presentation Demo: Slide 13](img/24ee12faff1e176006376e2d3a008214.png)

### 幻灯片 14

在最后一张幻灯片中，使用 WebSlides 的另一个内置 CSS 动画，即`zoomIn`:

```
<section class="zoomIn bg-black">
  <span class="background" style="background-image:url('https://source.unsplash.com/fPkvU7RDmCo/800x600')"></span>
  <div class="wrap">
    <h2 class="aligncenter">Start Coding SVG Today!</h2>
  </div>
</section>
```

![WebSlides Presentation Demo: Slide 14](img/804c5a8fb2e9cae37ec9d1f53b1642af.png)

恭喜你！你完了。你可以在这里看到最后的结果:

在 [CodePen](https://codepen.io) 上看 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )的 Pen [HTML 和 CSS 演示用 WebSlides](https://codepen.io/SitePoint/pen/KvmZJe/) 。