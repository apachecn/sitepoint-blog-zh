# 网站属性

> 原文：<https://www.sitepoint.com/scite-properties/>

SciTE 就像文本编辑器的 Firefox 快速、跨平台、可配置、免费/开源等。额外的好处是在 Linux 和 Windows 上提供了一个熟悉的环境，特别是在快捷方式方面。

如果你习惯了像 Windows 上的 [EditPlus](http://www.editplus.com) 或 [Ultra Edit](http://www.ultraedit.com/) 这样的编辑器，SciTE 至少是可配置的(行尾字符、制表符/空格等)。)并提供一个源代码“树形视图”,允许您“折叠”函数/类等。plus 拥有出色的代码完成能力。

在 Windows 上有很多预打包的[安装程序](http://scintilla.sourceforge.net/SciTEDownload.html)——我倾向于用[这个](http://amip.tools-for.net/SciTE-CVS.exe)(。这是基于最新的 SciTE CVS。在 Linux 上，通常使用适合发行版的包，尽管如果 GTK 可用的话，编译你自己的包没有问题。

还有一个配套的 [Filerx](http://llt.chez.tiscali.fr/) (仅限 Windows)，我没有使用，但增加了与“项目”相关的功能。

无论如何——我想把我的 SciTEGlobal.properties 文件转储到这里，让我和其他人的生活更轻松。配置可以应用于许多文件中，允许继承基本配置，同时单个用户可以进一步修改他们自己的设置。编辑你自己的最简单的方法是从菜单“选项”>“打开全局属性”

这个配置可能需要进一步的调整，特别是字体方面，但是已经按照我喜欢的方式设置好了基础，并且适合 PHP。如果你使用 SciTE 的“原始”版本，你可能需要从[这里](http://scintilla.sourceforge.net/SciTEExtras.html)获得 PHP 相关的附加组件。可以在[这里](http://scintilla.sourceforge.net/SciTEDoc.html)找到该配置中设置的文档。

 `# Global initialisation file for SciTE
# For Linux, place in $prefix/share/scite
# For Windows, place in same directory as SciTE.EXE (or Sc1.EXE)
# Documentation at [http://www.scintilla.org/SciTEDoc.html](http://www.scintilla.org/SciTEDoc.html)`

 `#全局

#窗口大小和可见度
if PLAT _ WIN
position . left = 0
position . top = 0
if PLAT _ GTK
position . left = 5
position . top = 22

position . width = 576
position . height = 740

position . tile = 1
# full . screen . hides . menu = 1
# minimize . to . tray = 1
# split . vertical = 1
# output . horizontal . size = 200
# output . vertical . size = 60
# horizontal . scroll bar = 0
# horizontal . scroll . width = 10000
# output . horizontal . scroll . width = 10000
# output . scroll = 0

#编辑窗格中的大小和可见性
line . margin . visible = 1
line . margin . width = 4
margin . width = 16
# fold . margin . width = 16
# fold . colour = # ff 0000
# fold . margin . highlight . colour = # 0000 ff
blank . margin . left = 4
# blank . margin . right = 4
buffered . draw = 1
# two-phase . draw = 0【T10。

# Element styles
# view . eol = 1
# control . char . symbol =。
caret . period = 500
view . white space = 1
view . indentation . white space = 1
view . indentation . guides = 1
highlight . indentation . guides = 1
caret . fore = # ff 0000
caret . width = 2
caret . line . back = # fffed 8
call tip . back = # fff 0 Fe
edge . column = 200
edge . mode = 0
edge . colour =。

# Checking
are . you . sure = 1
# are . you . sure . for . build = 1
# quit . on . close . last = 1
load . on . activate = 1
# save . on . deactivate = 1
are . you . sure . on . reload = 1
reload . preserves . undo = 1
# check . if . already . open = 1
default . file . ext =。PHP
title . full . path = 1
title . show . buffers = 1
save . recent = 1
save . session = 1
open . dialog . in . file . directory = 1
# strip . trailing . spaces = 1
# ensure . final . line . end = 1
# consistent . line . ends = 1
# save . deletes . first = 1
buffers = 50
buffers . zorder。

# Indentation
tabsize = 4
indent . size = 4
use . tabs = 0
indent . automatic = 1
indent . opening = 0
indent . closing = 0
# tab . indent = 0
# back space . un indents = 0
wrap = 1
# cache . layout = 3
# output . wrap = 1
# output . cache . layout = 3

#折叠
#启用折叠，折叠时显示下方线条。
fold = 1
fold . compact = 1
fold . flags = 16
fold . symbols = 1
fold . on . open = 1

# Find and Replace
# escapes . in . Find . Replace = 1
# findstr 在最近版本的 Windows 上可用包括 2000
if PLAT _ WIN
# Find . command = findstr/n/s $(Find . what)$(Find . files)
Find . command = grep-line-number " $(Find . what)" $(Find . files)
# Find . input = $(Find . what)
if PLAT _ GTK
Find . command = grepc *。cxx *。h
# find . replace . match case = 1
find . replace . escapes = 1
# find . replace . regexp = 1
# find . replace . regexp . POSIX = 1
# find . replace . wrap = 0
# find . replace with . focus = 0

# behavior
eol . mode = LF
# eol . auto = 1
clear . before . execute = 0
# VC . home . key = 1
wrap . aware . home . end . keys = 1
autocomplete word . automatic = 1
autocomplete . choose . single = 0
caret . policy . xslop = 1
caret . policy . width = 20
caret . policy . xstrict = 0
caret . policy . xeven = 0

#状态栏
Status Bar . number = 4
Status Bar . text . 1 =
Li = $(line number)co = $(column number)$(OverType)($(eol mode))$(file attr)
Status Bar . text . 2 =
$(buffer length)chars in $(nbof lines)行。Sel: $(SelLength)字符。
statusbar.text.3=
现在是:Date = $(current Date)Time = $(current Time)
status bar . text . 4 =
$(filename ext):$(FileDate)` 

## 分享这篇文章