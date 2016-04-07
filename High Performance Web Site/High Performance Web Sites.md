#Hign Performance Web Sites
##Rules
###1.减少HTTP请求

减少页面组件请求（**images**，**scripts**，**stylesheets**，**flash**）

方法：

- Image Maps
- CSS Sprites
- Inline Images
- Combined Scripts and Stylesheets

####Image Maps

适用情况：页面上有多个图片包含超链接

Image Maps把页面上分别获取多个独立图片改成获取一张多个图片组合后的大图，各个图标显示大图的指定位置部分，最终只发一次HTTP请求

![](./imagemap.gif)

```
<img usemap="#map1" border=0 src="/...gif">
<map name="">
	<area shape="rect" coords="x1,y1,x2,y2" href="...html">
	<area shape="rect" coords="x1,y1,x2,y2" href="...html">
	...
</map>
```

弊端：硬编码、容易错，而且只能用矩形图标

####CSS Sprites

原理同上，不过是用css实现

```
<div style="background-image:url('...gif');background-positive:-x px -y px";width:w px;height:h px>
此处x是大图（左上为0,0）中x偏移量，y是y偏移量，w、h分别为长宽
```

**因为大图减少了图片的头部信息，所以实际上组合之后的大图大小会小一些**

####Inline Images

直接将图片数据内嵌在html页面里，格式是`data:[<mediatype>][;base64],<data>`

```
<IMG ALT="Red Star"
SRC="data:image/gif;base64,R0lGODlhDAAMALMLAPN8ffBiYvWW
lvrKy/FvcPewsO9VVfajo+w6O/zl5estLv/8/AAAAAAAAAAAAAAAACH5BAEA
AAsALAAAAAAMAAwAAAQzcElZyryTEHyTUgknHd9xGV+qKsYirKkwDYiKDBia
tt2H1KBLQRFIJAIKywRgmhwAIlEEADs=">
```

弊端：内嵌的图片不会被浏览器缓存，所以频繁使用的图片不要这么做。比较好的做法是将背景图片内嵌，并且样式写在外部链接的样式文件中，这样会被缓存。如：

```
.home { background-image: url(data:image/gif;base64,R0lGODlhHwAfAPcAAAAAAIxKA...);}
.gift { background-image: url(data:image/gif;base64,R0lGODlhHwAfAPcAAAAAAABCp...);}
.cart { background-image: url(data:image/gif;base64,R0lGODlhHwAfAPcAAAAAADlCr...);}
.settings { background-image: url(data:image/gif;base64,R0lGODlhHwAfAPcAAAAAA...);}
.help { background-image: url(data:image/gif;base64,R0lGODlhHwAfAPcAAAAAALW1t...);}
```

####Combined Scripts and Stylesheets

 将css和script文件合到一起，可以有效减少请求数量

---
> *Make fewer HTTP requests.*

###2.使用CDN
####CDN
将静态文件（css，js，image，flash）存在地域分布式（页面组件）服务器上

---
> *Use a content delivery network.*

###3.标头添加过期时间
####过期标头
```
Expires: Thu, 15 Apr 2010 20:00:00 GMT
```

在过期时间前，浏览器会使用缓存下来的Response
####最大时长和mod_expires
```
Cache-Control: max-age=315360000
```

**HTTP/1.1**中增加`Cache-Control`标头来代替`Expires`，因为`Expires`使用固定日期，会造成服务器端和客户端时间同步的问题，而且每次请求浏览器都会做一次检查，服务器端也需要重新生成新的过期日期

在同时使用`Expires`和` Cache-Control`时，后者会覆盖前者

**mod_expires**模块根据MIME类型和时长设置（年月日等），自动生成`Expires`和`max-age`

```
<FilesMatch "\.(gif|jpg|js|css)$">
ExpiresDefault "access plus 10 years"
</FilesMatch>
```

以下是在请求到达时，服务器生成的

```
Expires: Sun, 16 Oct 2016 05:43:02 GMT
Cache-Control: max-age=315360000
```

####Empty Cache vs. Primed Cache
适用于一次会话，多个页面的情况

####不仅限于图片
js、css、flash、image应该设置长过期时间，html则不然
####修改文件名
在页面组件被修改时，修改文件名以让用户请求最新版本

在名称中包含版本号

---
> *Add a far future Expires header to your components.*

###4.压缩组件