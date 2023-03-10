# 使用 JavaScript 创建地理空间和高级地图

> 原文：<https://www.sitepoint.com/javascript-geospatial-advanced-maps/>

地理空间信息系统(GIS)是地图学和信息技术的一个领域，涉及地理和空间数据的存储、操作、分析和表示。您可能最熟悉 GIS 服务，这些服务可以生成动态的二维平铺地图，自 [MapQuest](http://en.wikipedia.org/wiki/MapQuest) 时代起，这些地图就在 web 上占据显著地位。

直到最近，开发 2D 地图之外的地理空间应用程序还需要综合的 GIS 服务，如 ArcGIS、诺基亚地图或谷歌地图。虽然这些 API 功能强大，但它们也很昂贵，学习起来很麻烦，并且将地图开发者局限于单一解决方案。幸运的是，现在有大量有用的开源 JavaScript 工具来处理高级制图和地理空间分析。

在本文中，我将研究如何用 JavaScript 和 HTML 实现 GIS 技术，重点关注用于特定任务的轻量级工具。我将介绍的许多工具都基于 Mapbox、CloudMade 和 MapZen 等服务，但这些都是模块化的库，可以作为包添加到 Node.js 中，或者用于在 web 浏览器中进行分析。

**注意**:这篇文章中嵌入的 CodePen 示例最好直接在 CodePen 上查看。

## 几何图形和 3D

### 距离和测量

拥有小型、专注的库来执行距离测量和转换操作尤其有用，例如查找[地理围栏](http://en.wikipedia.org/wiki/Geo-fence)的面积或将英里转换为千米。以下库处理代表地理空间的 [GeoJSON](http://geojson.org/) 格式的对象。

*   **[Geolib](https://github.com/manuelbieh/Geolib)** 提供两个纬度-纬度坐标之间的距离(和估计时间)计算。Geolib 的一个便利特性是[按距离排序](https://github.com/manuelbieh/Geolib#geoliborderbydistanceobject-latlng-mixed-coords)，它按距离对列表或数组进行排序。该库还支持提升。
*   下一节介绍的 **[Turf.js](http://turfjs.org/)** 也提供了一个距离函数来计算两点之间的[大圆距离](http://en.wikipedia.org/wiki/Great-circle_distance)。此外，Turf.js 还计算面积、沿路径的距离以及两点之间的中点。
*   **[Sylvester](https://github.com/jcoglan/sylvester)** 是一个 JavaScript 中的几何、向量和矩阵数学库。当直线和平面的基本测量不够时，这个库是有用的。

### 三维（three dimension 的缩写）

虽然上述库适用于地理的 2D 投影，但三维 GIS 是一个令人兴奋和广阔的领域-这很自然，因为我们生活在 3D 空间中。幸运的是，WebGL 和 HTML5 canvas 也为 web 开发人员开辟了新的 3D 技术。

*   **[Three.js](https://github.com/mrdoob/three.js/)** 是一个针对几何和网格对象的 JavaScript 库。对 Three.js 的 [Three GeoJSON 扩展](https://github.com/jdomingu/ThreeGeoJSON)提供了一种在 3D 平面和球体上渲染 GeoJSON 对象的简单方法。
*   **[OSM 建筑项目](http://osmbuildings.org/)** 允许地图设计者在 2D 地图上将建筑表现为 3D 对象。该项目使用 OpenLayers 和传单。这可以发挥很大的作用，例如汤姆·霍尔德内斯[用 OSM 的建筑绘制了伦敦的地图](https://github.com/talltom/3DLondon)。

以下是如何在 3D 对象上显示 GeoJSON 要素的示例:

参见 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )用 Three.js 绘制的 Pen [3D 地图球体。](http://codepen.io/SitePoint/pen/YXyvKz/)