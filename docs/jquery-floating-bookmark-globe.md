# jQuery 浮动书签 Globe–非常有趣！

> 原文：<https://www.sitepoint.com/jquery-floating-bookmark-globe/>

![jquery-bookmark-fun](img/1d459515cfc8ffa702fa1cb8349cb57c.png "jquery-bookmark-fun")

有趣的浮动书签地球仪使用了一些非常聪明的 JS/CSS 代码，我不会去解释(但是如果你想看，我把它放在下面了！).它是基于 katamari 球类游戏，在那里它四处移动并捡起东西！按住鼠标右键时，书签会在页面上浮动。

## 你自己试试

1.  访问您的网站
2.  将下面的代码复制到地址栏中
3.  点击欣赏:)

```
var _0x6e06=["x68x74x74x70x3Ax2Fx2Fx63x6Fx64x65x2Ex6Ax71x75x65x72x79x2Ex63x6Fx6Dx2Fx6Ax71x75x65x72x79x2Dx6Cx61x74x65x73x74x2Ex6Ax73","x68x74x74x70x3Ax2Fx2Fx6Bx61x74x68x61x63x6Bx2Ex63x6Fx6Dx2Fx6Ax73x2Fx6Bx68x2Ex6Ax73","x67x65x74x53x63x72x69x70x74","x72x65x61x64x79"];jQuery(document)[_0x6e06[3]](function (){jQuery[_0x6e06[2]](_0x6e06[0],function (){jQuery[_0x6e06[2]](_0x6e06[1]);} );} );
```

或者在 Firebug 中运行:

```
jQuery(document).ready( function()
{
	jQuery.getScript('https://code.jquery.com/jquery-latest.js', function() {
		jQuery.getScript('http://kathack.com/js/kh.js');
	});
});
```

## 或者如果你只是想看到事情爆炸…

```
jQuery(document).ready( function()
{
	jQuery.getScript('https://code.jquery.com/jquery-latest.js', function() {
		jQuery.getScript('http://erkie.github.com/asteroids.min.js');
	});
});
```

## JavaScript 代码

 `版权所有亚历克斯·莱昂，大卫·努弗，大卫·张，2011-03-11。kathack.com

javascript:var i，s，ss =[' http://kat hack . com/js/KH . js '，' https://Ajax . Google APIs . com/Ajax/libs/jquery/1 . 5 . 1/jquery . min . js ']；for(I = 0；我！=ss .长度；i++){ s = document . createelement(' script ')；s . src = ss[I]；document . body . appendchild；} void(0)；

*/
var BORDER _ STYLE = " 1px solid # BBB "，
CSS_TRANSFORM = null，
CSS_TRANSFORM_ORIGIN = null，
POSSIBLE _ TRANSFORM _ PREFIXES =['-WebKit-'，'-moz-'，'-o-'，'-ms-'，"]，
KH first = false；

/*在一个页面上运行两次时，更新可选择的节点，而不是
*创建更多。
*/
如果(！window . khnodes){
KH first = true；
window . khnodes = new sticky nodes()；
}

函数 getCssTransform() {
var i，d = document.createElement('div ')，pre
for(I = 0；I 0){
textels . push(El)；
返回；
}
如果(！El . child nodes | | El . khignore){
return；
}
should add = shouldAddChildren(El)；
for (i = 0，len = El . child nodes . length；I 0&ws[0]。length = = = 0){
ws . shift()；
}
for(I = 0；I 0){
n = document . createelement(' span ')；
n . innerhtml = words[I]；
p.insertBefore(n，textEl)；
addDomNode(n)；
}
if(I 0){
n = document . create text node(ws[I])；
p.insertBefore(n，textEl)；
}
}
p . remove child(textEl)；
}

buildTextEls(el，should addchildren(El))；
textels . map(wordstosspans)；
}；

/*包括 el。*/
this . addtagnames = function(El，tagNames){
var tname = El . tagname&&El . tagname . tolowercase()，
i，j，els，len
如果(el.khIgnore) {
返回；
}
if(tagnames . index of(tname)！= =-1){
adddom node(El)；
}
如果(！El . getelementsbytagname){
return；
}
for(I = 0；如果对象应该被移除，则为真。
*/
this . remove intersect = function(x，y，r，cb) {
var xi，yi，arr，I，r2 = r * r，go，
startXI = math . floor((x–r)/GRIDX)，
startYI = math . floor((y–r)/GRIDY)，
endXI = math . floor((x+r)/GRIDX)+1，
endYI = math . floor((y+r)/GRIDX)
for(Xi = start Xi；xi getVol()) {
返回 false
}
attachGridObj(go)；
还真；
}

this . update physics = function(){
var oldX = x，oldY = y，dX，dY，
bounce = false，
accelTh；
if(Accel){
Accel th = math . atan2(Accel targety–y，Accel targetx–x)；
VX+= math . cos(accelh)* 0.5；
vy+= math . sin(Accel th)* 0.5；
} else {
VX * = 0.95；
vy * = 0.95；
}
x+= VX；
y+= vy；
/*在文件边缘弹跳球。*/
if(x–radius docW){
bounce = true；
x = docW–radius–1；
VX =-VX；
}
if(y–radius docH){
bounce = true；
y = docH–radius–1；
vy =-vy；
}
如果(vx！== 0 || vy！== 0) {
th = Math.atan2(vy，VX)；
dx = x–oldX；
dy = y–oldY；
/* arclen = th * r，所以 th = arclen/r . */
phi-= math . sqrt(dx * dx+dy * dy)/radius；
}
sticky nodes . remove intersect(x，y，radius，remove intcb)；
this . draw()；
if(bounce&sounds){
sounds . play _ bounce()；
}
}；

函数 drawBall() {
var sx1，sy1，sx2，sy2，dx，dy，I，pct1，pct2，z1，z2；
/*移动/调整画布元素的大小。*/
canvas _ El . style . left =(x–radius)+' px '；
canvas _ El . style . top =(y–radius)+' px '；
如果(半径！= lastR){
canvas _ El . width = 2 * radius+1；
canvas _ El . height = 2 * radius+1；
lastR =半径；
}
/*画白圈。*/
canvas_ctx.clearRect(0，0，2 * radius，2 * radius)；
canvas _ CTX . fill style = " # fff "；
canvas _ CTX . begin path()；
canvas_ctx.arc(半径，半径，半径–1，0，数学。PI * 2，真)；
canvas_ctx.fill()。
/*绘制外边框。*/
canvas _ CTX . stroke style = color；
canvas _ CTX . begin path()；
canvas_ctx.arc(半径，半径，半径–1，0，数学。PI * 2，真)；
canvas_ctx.stroke()。
/*画条纹。*/
canvas _ CTX . fill style = color；
sx1 = radius+radius * Math . cos(th+Math。PI/16)；
sy1 = radius+radius * Math . sin(th+Math。PI/16)；
sx2 = radius+radius * Math . cos(th–Math。PI/16)；
sy2 = radius+radius * Math . sin(th–Math。PI/16)；
dx =(radius+radius * Math . cos(th+Math。PI * 15/16))–sx1；
dy =(radius+radius * Math . sin(th+Math。PI * 15/16))–sy1；
for(I = 0；I 0&z2>0){
canvas _ CTX . begin path()；
canvas _ CTX . move to(sx1+PC t1 * dx，sy1+PC t1 * dy)；
canvas _ CTX . line to(sx1+pct 2 * dx，sy1+pct 2 * dy)；
canvas _ CTX . line to(sx2+pct 2 * dx，sy2+pct 2 * dy)；
canvas _ CTX . line to(sx2+PC t1 * dx，sy2+PC t1 * dy)；
canvas_ctx.fill()。
}
}

/* *
* @如果附着对象大致可见，则返回 true。
*/
函数 draw attached(att){
var oth = th+att . off th，
ophi = phi + att.offPhi，
ox = att.r * Math.cos(oth)，
oy = att.r * Math.sin(oth)，
dx =(att . r * Math . cos((th–att . off th)+Math。PI))–ox，
dy =(att . r * Math . sin((th–att . off th)+Math。PI)–oy，
pct = (-Math.cos(ophi) + 1) / 2，
cx = ox + pct * dx，
cy = oy + pct * dy，
oz = att . r * math . sin(ophi)；
如果(oz 0)？501 : 499;
att . El . style . set property(
CSS _ TRANSFORM，
' translate("+x+' px，'+y+' px)'+
' rotate("+th+' rad)'+
' scaleX("+math . cos(ophi)+')'+
att . attt，null)；
还真；
}

函数 onattacheddemoved(att){
attached div . remove child(att . El)；
删除附件 El；
}

this.draw = function () {
var i，att，numattacheddvisible = 0；
drawBall()；
for(I = attached . length；–I>= 0；){
att = attached[I]；
if(att . remover MAX _ ATTACHED _ VISIBLE){
/*移除旧项目并停止。*/
attached.splice(0，I)。map(onattacheddremoved)；
破；
}
}
}
}；
}

函数 prevent default(event){
event . prevent default()；
event . return value = false；
返回假；
}

函数 Game(gameDiv，stickyNodes，ballOpts) {
var stickyNodes，player1，physicsInterval，resizeInterval，listeners =[]；
player 1 = new player ball(gameDiv，stickyNodes，ballOpts，false)；
player 1 . init()；
player1.setXY(300，300)；
window.scrollTo(0，200)；

函数 on _ resize(){
player 1 . setdocsize(jQuery(document)。width()–5，
jQuery(文档)。height()–5)；
}
on_resize()。

/*触摸事件–始终开启？*/
document . addevent listener(' touch start '，function(event){
if(event . touches . length = = = 1){
player 1 . set Accel(true)；
return preventDefault(事件)；
}
}，真)；
document . addevent listener(' touch move '，function(event){
player 1 . setacceltarget(event . touches[0])。pageX，
event.touches[0]。佩吉)；
}，真)；
document . addevent listener(' touchend '，function(event){
if(event . touches . length = = = 0){
player 1 . set Accel(false)；
返回 preventDefault(事件)；
}
}，真)；

如果(ballOpts。MOUSEB！== -5) {
/*鼠标按键*/
document . addevent listener(' mousemove '，function(event){
player 1 . setacceltarget(event . pagex，event . pagey)；
}，真)；
document . addevent listener(' mousedown '，function(event){
if(event . button = = = ballOpts。MOUSEB){
player 1 . set Accel(true)；
return preventDefault(事件)；
}
}，真)；
document . addevent listener(' mouseup '，function(event){
if(event . button = = = ballOpts。MOUSEB){
player 1 . set Accel(false)；
返回 preventDefault(事件)；
}
}，真)；

如果(ballOpts。MOUSEB === 0) {
/*阻止点击事件。*/
document . addevent listener(' click '，function(event){
if(event . button = = = 0){
return prevent default(event)；
}
}，真)；
} else if (ballOpts。MOUSEB === 2) {
/*阻止右击上下文菜单。*/
document . addevent listener(' context menu '，preventDefault，true)；
}
}

physics interval = setInterval(function(){
player 1 . update physics()；
}，25)；
resize interval = setInterval(on _ resize，1000)；
}

函式 whenally loaded(gamediv，popup，sticky nodes){
sticky nodes . final(jquery(document)。width()、jQuery(文档)。height()；
jquery(# load ingp ')。空白()；
jquery(<button>开始！</button>’。点击(函数(){
var 游戏、bgmusic、ballopts
if(jquery(# bgmusic ')。attr(' checked '){
if(！(bgmusic = document . getelement byid(' khbgmusic '){
bgmusic = document . create element(' audio ')；
bgmusic . id =‘khbgmusic’；
bgmusic . loop =‘loop’；
bgmusic . src =‘http://kathack . com/js/kata mari . MP3’；
游戏 div . appendchild(bgmusic)；
}
bgmusic . play()；
}
球选项= {
颜色:jquery(# khcolor ')。val()，
vol _ mult:parse float(jquery(# vol _ mult ')。val()，
max _ attached _ visible:parse int(jquery(# maxatt ')。val()、10)、
check _ vol:(jquery(‘# check v ')。attr('checked ')属性？true : false，
moueb:parse int(jquery(# moueb ')。val()、10)
}；
游戏 Div.removeChild(弹出)；
游戏=新游戏(gameDiv、stickyNodes、ball opts)；
}。附录(“# loadingp”)；

function build popup(game div){
var d = document . createelement(' div ')，b；
d.style.cssText = '
位置:固定；
左:50%；
最高:50%；
宽度:400px
左边距:-200 px；
边距-顶部:-150 px；
边框:1px 纯黑；
背景色:白色；
颜色:黑色；
填充:20px
字体大小:13px
文本对齐:左对齐；z 指数:501；'；
d.innerHTML = '

# [地籍！](http://kathack.com/)

<button style="position:absolute;top:0;right:0;">X</button>

操控:按住 **<select id="mouseb"><option value="0">左键点击</option> <option value="2" selected="selected">右键点击</option> <option value="-5">触摸</option></select>** 来控制球！

<label>Background Music?
<input id="bgmusicc" type="checkbox" checked="checked"></label><label>Katamari Color: <select id="khcolor"><option value="#ff0000" style="background-color:#ff0000;color:#ff0000">r</option> <option value="#00ff00" style="background-color:#00ff00;color:#00ff00">g</option> <option value="#0000ff" style="background-color:#0000ff;color:#0000ff">b</option> <option selected="selected" value="#7D26CD" style="background-color:#7D26CD;color:#7D26CD">p</option></select></label>
 <label title="Lower this if the game gets slow.">Max Attached Objects: <select id="maxAtt"><option>25</option><option>50</option><option selected="selected">75</option><option>100</option><option>9000</option></select></label>
 <label title="How much to grow when an object is picked up.">Growth Speed: <input id="vol_mult" type="text" size="6" value="1.0"></label> 
 <label title="Bigger objects require a bigger katamari to pick up.">Realistic Pickups? <input id="checkv" type="checkbox" checked="checked"></label> 

正在加载！

；
gamediv . appendchild(d)；
d . getelementsbytagname(' button ')[0]。addEventListener('click '，function(){
gamediv . remove child(d)；
}，真)；
返回马超；
}

function main() {
var gameDiv，checkInterval，stickyNodes，popup

game div = document . createelement(' div ')；
gamediv . khignore = true；
document . body . appendchild(game div)；
popup = build popup(gameDiv)；

/* setTimeout 以便在冻结前显示弹出窗口。*/
setTimeout(function(){
var I，len，El；
window . KH nodes . add words(document . body)；
for (i = 0，len = document . body . child nodes . length；一. http://kathack.com/` 

## `分享这篇文章`