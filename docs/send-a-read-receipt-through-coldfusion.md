# 通过 ColdFusion 发送已读回执

> 原文：<https://www.sitepoint.com/send-a-read-receipt-through-coldfusion/>

*…发帖人* [大卫·梅德洛克](https://www.sitepoint.com/forums/member.php?u=24385) *:*

这里有一个使用 CFMAIL 的有趣的，甚至可能是有用的小花絮。你是否曾经在 Outlook 中收到一封电子邮件，并在阅读后看到一个小弹出窗口，上面写着“发件人要求此邮件的已读回执”。现在要发一个吗？”(或类似的东西…)嗯，这是通过邮件头实现的，您可以在 ColdFusion 中非常容易地复制它:

 `<cfmail to="<a href=" mailto:david.medlock="">david.medlock@example.com" from="[test@example.com](mailto:test@example.com)" server="mail.0web-hosting.com" subject="test read receipt">
<cfmailparam name="Read-Receipt-To" value="<a href=" mailto:test="">test@example.com"></cfmailparam> <cfmailparam name="Disposition-Notification-To" value="<a href=" mailto:test="">test@example.com">
This is a test for read receipts.</cfmailparam></cfmail>` 

这很管用。甚至可以要求收件人将已读回执发送给卖家以外的人。(我还没有尝试过，但是理论上应该可以，因为它是一个独立于发送者地址的头……)

尽情享受吧！(也不要给我发什么已读回执，我讨厌那些东西……)

## 分享这篇文章