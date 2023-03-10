# 上传关怀:你的超级动力图像 CDN

> 原文：<https://www.sitepoint.com/uploadcare-image-cdn/>

*本文由[上传关怀](https://uploadcare.com/?coupon=sp-2017&utm_campaign=sp-discount)赞助。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

一个好的内容交付网络将提升你的网站的性能。文件托管在全球服务器的不同域或子域上，以确保用户无论身在何处都能获得最快的下载。最好的 cdn 将有助于管理您的数据并节省托管成本。

cdn 通常用于托管静态资产，如图像。您将一个图像文件推送到 CDN 服务器，然后在页面的`<img>`标签中引用它。这是大多数人所能做到的，但是 [Uploadcare](https://uploadcare.com/) 将 CDN 服务带到了另一个水平，可以节省你大量的开发时间。

## 上传服务

[Uploadcare](https://uploadcare.com/) 提供[账户](https://uploadcare.com/pricing/)从免费(每月 500MB 空间和 5GB 流量)到支持高达 5TB 文件大小和几乎无限带宽的白标商业服务。所有计划包括:

### [可配置上传小工具/对话框](https://uploadcare.com/documentation/widget/)

尽管 HTML5 有所进步，但将文件上传到服务器需要大量的开发工作。上传小部件为您的站点提供了一个简单的、现成的、可样式化的组件。它允许用户从他们的本地文件系统、相机、URL、Google Drive、Google Photos、Dropbox、OneDrive、脸书、Instagram、Evernote 和 Flickr 中直接拖放或选择文件到 Uploadcare CDN(列表一直在增长，您也可以轻松添加自定义源):

![Uploadcare's customizable widget](img/dd93638eb61aad2f7848c3a763671588.png)

### [REST API](https://uploadcare.com/documentation/upload/)

提供了 REST API，因此任何语言或系统都可以批量上传和管理文件。上传一个简单的 cURL 例子`image.jpg`:

```
curl -F "UPLOADCARE_PUB_KEY=demopublickey" \
     -F "UPLOADCARE_STORE=1" \
     -F "file=@image.jpg" \
     "https://upload.uploadcare.com/base/" 
```

JSON 响应返回分配给所有文件的通用唯一标识符(UUID ),以便以后可以引用它们:

```
{
  "file": "17be4678-dab7-4bc7-8753-28914a22960a"
} 
```

### [CDN API](https://uploadcare.com/documentation/cdn/)

任何创建响应式 web 和移动应用的人都会认为这个特性是必不可少的。一旦图片上传完毕，就可以单独通过 URL 来调整大小和操作。最好用一个例子来说明这一点。考虑一下这个戴着耳机的家伙的形象(谢谢 Alex bljan 在[Unsplash.com](https://unsplash.com/)提供的)。我上传了一个 1600 x 900px，140KB 的文件到 Uploadcare:

![Example image of man with earphones](img/0f0dcea8a9193225f3494ab861eefc59.png)

产生的 URL 包含图像的 UUID:

`https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/`

你可以在末尾添加任何文件名来帮助你记住你正在使用的图片，并帮助搜索引擎优化。

`https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/man.jpg`。

暂时忽略高密度视网膜屏幕的影响，在一个 500 像素宽的屏幕上使用这种全尺寸图像没有什么意义。通常情况下，我需要使用 Photoshop 或构建系统来创建这个图像的大小调整版本，但 Uploadcare 取消了这一要求。我只需将网址改为:

`https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/resize/500x/man.jpg`

这立即提供了一个 500 像素宽的图像，它现在的文件大小小于 20KB:

![Example image resized](img/1880e6bdc5bcd797d6046d92e56a884f.png)

Uploadcare 声称具有超快的调整大小速度，通常比 ImageMagick 快 15-30 倍(而且麻烦少得多！)

或者，我可以使用 URL 更改图像高度，例如:

`https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/resize/x300/man.jpg`

或者，我的网站可能需要一个 100x100px 的最大压缩率的灰度缩略图？网址:

`https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/scale_crop/200x200/-/grayscale/-/quality/lightest/-/progressive/yes/-/format/auto/man.jpg`

图像文件大小现已降至 3KB 以下。在基于 Blink 的浏览器中，包括 Chrome、Opera、Vivaldi 和 Brave，`/format/auto/`指令检测 WebP 支持并返回一个更小的 2.3KB 图像。

![Example image as thumbnail](img/9fc87cc1d9ace89eb66467d543a2d55c.png)

CDN API URL 可以包含任意数量的由分隔符`/-/`分隔的处理指令。[文档](https://uploadcare.com/documentation/cdn/)提供了以下内容的完整说明:

*   更改图像格式、质量和渐进式加载
*   创建缩略图、调整大小、裁剪和拉伸
*   滤镜，如增强、锐化、模糊、灰度和反转
*   旋转、翻转和镜像
*   添加图像叠加、提取流行颜色等

你甚至可以[检测人脸](https://uploadcare.com/documentation/cdn/#detect_faces)和[将动画 gif 转换成视频](https://uploadcare.com/documentation/cdn/#gif2video)，以节省带宽而不损失质量。

## 将 CDN API 用于响应图像

CDN API 也使得定义响应图像变得容易。假设一个站点想要在一个 100%宽度的 hero 块中使用上面的图像，其大小适合于小于 400 像素、800 像素、1200 像素或 1600 像素及以上的屏幕。这是使用`<img>`标签上的 HTML5 `srcset`属性设置的，它定义了一个逗号分隔的列表，包含:

1.  图像文件名后跟一个空格，然后
2.  以像素为单位的图像固有宽度，例如`400w`–注意`w`单位，而不是更典型的`px`。

因此,`srcset`应该是:

```
https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/resize/400x/man.jpg 400w,
https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/resize/800x/man.jpg 800w,
https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/resize/1200x/man.jpg 1200w,
https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/resize/1600x/man.jpg 1600w 
```

*(添加回车以帮助阅读)*

如果英雄是*而不是* 100%宽，我还需要设置一个`sizes`属性。这将定义一个逗号分隔的列表，其中包含:

1.  媒体条件，例如`(max-width: 400px)`，后跟一个空格，然后是:
2.  当条件为真时，图像将填充的宽度，例如`100%`或`400px`。

我还需要设置标准的`src`属性来引用浏览器不支持`srcset`时使用的图像。图像的中点 800 像素版本可能是分辨率和文件大小的一个很好的折衷。最终图像代码:

```
<img srcset="https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/resize/400x/man.jpg 400w,
             https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/resize/800x/man.jpg 800w,
             https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/resize/1200x/man.jpg 1200w,
             https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/resize/1600x/man.jpg 1600w"
        src="https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/resize/800x/man.jpg"
        alt="man with headphones" /> 
```

参见[responsive-image.html](https://codepen.io/craigbuckler/pen/POBZwX)

Uploadcare 上的一张图片立刻提供了四种可能的选择。如果用户的设备宽度为 1000 像素，他们将获得 80KB、1200 像素的图像，并节省 60KB 的带宽。该网站的渲染速度也更快，反应也更灵敏。

## 使用 CDN API 进行艺术指导

上面的例子假设我想使用相同的图像尺寸。然而，图像的右半部分很大程度上是不必要的，在较小的屏幕上，用[裁剪](https://uploadcare.com/documentation/cdn/#operation-crop)或[缩放 _ 裁剪](https://uploadcare.com/documentation/cdn/#operation-scale-crop)操作将图像分割成头像是可行的，例如

`https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/scale_crop/400x400/man-cropped.jpg`

![Example Image cropped](img/58398a7e3179f243a4fd9d324395997b.png)

然后用多个`<source>`子元素定义一个`<picture>`元素，这些子元素像以前一样设置媒体查询和`srcset`，即

```
<picture>
  <source media="(max-width: 599px)"
          srcset="https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/scale_crop/400x400/man-cropped.jpg 400w,
                  https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/scale_crop/600x600/man-cropped.jpg 600w" />
  <source media="(min-width: 600px)"
         srcset="https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/resize/800x/man.jpg 800w,
                 https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/resize/1200x/man.jpg 1200w,
                 https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/resize/1600x/man.jpg 1600w" />
       <img src="https://ucarecdn.com/718f79c5-2868-41c8-b56e-c87237674adb/-/resize/800x/man.jpg"
            alt="man with headphones" />
</picture> 
```

在本例中，为用户提供了:

*   浏览器宽度小于等于 599 像素时的方形头像(必要时使用 400x400px 像素或 600x600px 像素的图像)
*   浏览器宽度为 600 像素或更大时的完整图像(根据需要使用 800 像素、1200 像素或 1600 像素的图像)
*   一个 800 像素宽的完整图像，作为旧浏览器的后备。

参见[responsive-picture.html](https://codepen.io/craigbuckler/pen/bYjpdw)

Uploadcare 立即提供了所有五个选项，无需我们手动创建图像。

## 将 CDN API 用于渐进式图像加载器

我最近写了关于[如何构建自己的渐进式图像加载器](https://www.sitepoint.com/how-to-build-your-own-progressive-image-loader/)，它通过延迟加载图像来节省带宽。图像的表示是由一个微小的缩略图生成的。该文件可以是 20px 宽，小于 1KB，但它被放大和模糊以填充容器。当容器滚动到视图中时，加载并显示高分辨率图像。它适用于所有浏览器(或者当 JavaScript 失败时)，因为如果加载不成功，模糊的图像会链接到高分辨率版本。

Uploadcare CDN API 克服了与渐进式图像加载器相关的几个问题:

*   只有一个图像文件需要管理
*   没有必要手动创建一个小缩略图
*   图像长宽比应该总是匹配的(这对于确定容器的大小是必不可少的)
*   不需要图形应用程序或构建系统！

请参阅:密码笔。IO[https://codepen.io/craigbuckler/pen/yPqLXW](https://codepen.io/craigbuckler/pen/yPqLXW)或[https://cdn . raw git . com/Craig buckler/progressive-image . js/7f 022 AAF/demo . html](https://cdn.rawgit.com/craigbuckler/progressive-image.js/7f022aaf/demo.html)

代码也可以从 GitHub 下载[。](https://github.com/craigbuckler/progressive-image.js)

(注意，我对原文做了一些改动。我曾打算使用[路口观察器](https://www.w3.org/TR/intersection-observer/)，但似乎[可能还不实用](https://www.quirksmode.org/blog/archives/2017/10/intersection_ob.html)……)

```
 <a href="https://ucarecdn.com/1b73ebf8-b2d6-40cc-abd6-945d4df883c9/mountain.jpg"
 data-srcset="https://ucarecdn.com/1b73ebf8-b2d6-40cc-abd6-945d4df883c9/-/resize/400x/mountain.jpg 400w,
              https://ucarecdn.com/1b73ebf8-b2d6-40cc-abd6-945d4df883c9/-/resize/800x/mountain.jpg 800w, https://ucarecdn.com/1b73ebf8-b2d6-40cc-abd6-945d4df883c9/mountain.jpg 1600w"
       class="primary progressive replace">
    <img src="https://ucarecdn.com/1b73ebf8-b2d6-40cc-abd6-945d4df883c9/-/resize/32x/-/grayscale/-/quality/lightest/mountain.jpg"
       class="preview"
         alt="mountain" />
     </a> 
```

内部的`<img>`元素引用了由 Uploadcare CDN API 生成的微小灰度图像。它只有 378 字节，可以立即加载:

![thumbnail](img/c5f6a885a426234d0248ac80ae6798bd.png)

当图像占位符滚动到视图中时，JavaScript 代码删除缩略图并用响应图像替换它，同时从`<a>`元素复制数据:

```
<img srcset="https://ucarecdn.com/1b73ebf8-b2d6-40cc-abd6-945d4df883c9/-/resize/400x/mountain.jpg 400w,
             https://ucarecdn.com/1b73ebf8-b2d6-40cc-abd6-945d4df883c9/-/resize/800x/mountain.jpg 800w, https://ucarecdn.com/1b73ebf8-b2d6-40cc-abd6-945d4df883c9/mountain.jpg 1600w"
        src="https://ucarecdn.com/1b73ebf8-b2d6-40cc-abd6-945d4df883c9/mountain.jpg" alt="mountain"> 
```

加入一点 CSS3 动画，页面就有了令人愉悦的效果。

## 尝试上传关怀

[Uploadcare](https://uploadcare.com/?coupon=sp-2017&utm_campaign=sp-discount) 是一款超级强大的 CDN，可以轻松上传和管理文件。CDN API 使它成为任何实现响应式 web 设计的人的基本服务。有一个[免费、无义务的启动计划](https://uploadcare.com/accounts/create/free/)，他们还为 SitePoint 的读者提供[第一年 20%的优惠](https://uploadcare.com/?coupon=sp-2017&utm_campaign=sp-discount)。所以今天 *[报名！](https://uploadcare.com/accounts/signup/?coupon=sp-2017&utm_campaign=sp-discount)*

## 分享这篇文章