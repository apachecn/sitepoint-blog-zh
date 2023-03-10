# 当坏软件杀死

> 原文：<https://www.sitepoint.com/therac-25-bad-software-kills/>

[!["Snefru's Bent Pyramid in Dahshur" by Ivrienen at en.wikipedia. Licensed under CC BY 3.0 via Wikimedia Commons - http://commons.wikimedia.org/wiki/File:Snefru%27s_Bent_Pyramid_in_Dahshur.jpg#mediaviewer/File:Snefru%27s_Bent_Pyramid_in_Dahshur.jpg](img/02c0eaea32e61a76e4047c04962cf7b8.png)](http://commons.wikimedia.org/wiki/File:Snefru%27s_Bent_Pyramid_in_Dahshur.jpg#mediaviewer/File:Snefru%27s_Bent_Pyramid_in_Dahshur.jpg)

“达舒尔的斯涅夫鲁弯曲金字塔”

这就是“[弯曲金字塔](http://en.wikipedia.org/wiki/Bent_Pyramid "Bent Pyramid")”——一座有 4600 年历史的工程失败纪念碑。

从底部开始，两侧以惊人的 54 度陡坡开始，然后在大约一半的时候突然转向一个较平缓的 43 度斜坡。

据信，在南方约 60 公里处的另一座陡峭的金字塔——迈杜姆金字塔发生灾难性坍塌后，设计在施工过程中被修改了。

当然，这很难责怪古代金字塔建造者。他们实际上是在发明和学习工程学。

从那时起，有一件事一直没有改变:当结构工程师搞砸了，人们会受到伤害。我们不能确定，但是梅杜姆的坍塌不太可能在没有人员伤亡的情况下发生。

相比之下，“软件工程师”看起来更像是工程科学的一种味道。一个错误可能会阻止用户访问他们的帐户或输入信息，但它肯定不会危及生命？

没人会受伤，对吧？

或者这就是我们*认为的*。

事实是，每年我们的系统——从电力到交通到农业到紧急服务——都变得更加依赖我们所有人创建高质量的软件来支持它们。

当我们失败时——就像那些古埃及人一样——人们实际上会受到伤害。

令人惊讶的是，正如 Therac-25 的悲惨事件向我们展示的那样，这甚至不是 21 世纪的问题。

### 软件可以杀人

到 20 世纪 70 年代末，加拿大原子能有限公司(AECL)因制造放射治疗机器而赢得了良好的声誉。

这些机器使用定向电子束攻击病人体内的肿瘤。毫无疑问，这些光束是高强度的，具有潜在的致命性。

AECL 此前凭借其 Therac-6 和 Therac-20 型号取得了巨大成功。这些装置需要由训练有素的操作员手动控制，并使用机械开关和硬连线电路来确保高度的安全性。

Therac-25 是他们的“梦想机器”。

![The Therac-25 machine](img/3a2398230c98bb3d324b972906055bc0.png)

这台新机器比它的前辈更小更便宜，但效率更高，它结合了两种不同的光束技术——x 射线和高能电子。不同的光束允许操作者瞄准不同深度的肿瘤，而不损伤附近的健康组织。

Therac-25 既雄心勃勃又精密复杂——第一次所有这些硬件都由软件层控制。

不幸的是，尽管 AECL 的意图是好的，但他们的软件设计却糟糕透顶，包含了一系列可怕的设计缺陷。

后来的调查小心翼翼地记录了这些缺陷，它们今天读起来仍然令人不寒而栗。

在一个实例中，在治疗过程中，一台机器连续关机，每次都报告一个神秘的“ *H-tilt* ”和“ *no dose* ”错误信息。困惑的操作员在放弃之前尝试了六次进行治疗。

直到后来才确定这台机器确实每次都输送了全部剂量——一次灾难性的过量。

从 1982 年推出到 1986 年退出，有 6 名患者在 Therac-25 治疗中受到致命伤害。当你想到这些可怜的人已经病入膏肓的时候，这是非常可怕的。

今天，AECL 不是作为一家公司而存在，而是作为一个悲剧教科书的例子，向我们所有人展示了设计拙劣和未经测试的软件是如何影响生活的。直到今天，Therac-25 的悲剧仍然告诉我们很多关于系统设计和安全测试的想法。

[![Ancient pharaoh statue ](img/2d0012e88a7c50822bd144c2d4fc665b.png)](https://www.flickr.com/photos/kmf164/2597911527/)

图片: [kmf164](https://www.flickr.com/photos/kmf164/2597911527/)

即使你是一名前端设计师，并且不认为自己是一名“严肃的工程师”，Therac-25 也有重要的经验。虽然一些缺陷是由糟糕的编码过程造成的，但至少同样多的损害是由不充分的文档、无用的反馈和不可理解的错误消息造成的。这些是每个人——设计师、编码员、经理、UX 人和测试员——都应该有影响力的领域。

回顾那些古埃及人，很明显，他们从早期的错误中吸取了教训，并继续建造一些有史以来最令人惊叹的建筑。

软件工程仍然是一个相对年轻的领域——让我们希望我们已经建造了弯曲的金字塔。

*[最初发表于 1 月 29 日的 SitePoint 设计通讯](http://sitepointnewsletters.cmail2.com/t/ViewEmail/y/DCCE2F3F0710E617/92627AAC5401C27B05AF428974F65BCD "January 29th 2014 edition")。[订阅此处](https://www.sitepoint.com/newsletter/ "Sign up here") 。*

## 分享这篇文章