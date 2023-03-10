# WireShark–深入！(HTTP 请求分析)

> 原文：<https://www.sitepoint.com/wireshark-deep-http-request-analysis/>

![wireshark-logo](img/3663a76d0bb72228d9930d92b8e271b5.png "wireshark-logo")

Wireshark 是用于 Unix 和 Windows 的网络协议分析器。到目前为止，我只是用这个工具刷了一下表面，但是当[在你的机器和网络上的其他机器之间对 http 请求](http://www.jquery4u.com/testing/http-request-net-panel-httpfox-fiddler2/)执行分析时，它会派上用场。

## 开始之前

*   Wireshark 会捕获每个请求，因此当您准备好捕获时，只需单击开始，完成后单击停止
*   你需要你的 IP 地址

## 基本说明

1.  下载、安装、运行
2.  单击开始捕获请求
3.  使用表达式过滤器来查找您的请求，以下是一些常见的过滤器:
    1.  ip.addr == 10.27.999.99(例如)
    2.  ip.dst == 10.27.999.99(例如)
4.  找到您想要分析的请求，然后右键单击>跟随 TCP 流
5.  这应该会显示完整的请求

![wireshark-2](img/1717d309734133864cafc06ee3c95cde.png "wireshark-2")

![wireshark-3](img/454e36e1eecc3a462bfbae393275183f.png "wireshark-3")

## 分享这篇文章