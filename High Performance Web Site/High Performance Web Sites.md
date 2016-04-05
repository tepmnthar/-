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
