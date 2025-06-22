# html

## 标签

### 列表

有序列表 <ol>   无序列表<ul> 自定义列表<dl>

```html
<ol>
    
</ol>
<dl>
    <dt>标题</dt>
    <dd>内容</dd>
</dl>
```

### 表格

```html
<table>
	<caption></caption>
    <thead>
    	<tr>
        	<th></th>
        </tr>
    </thead>
    <tbody>
    	<tr>
        	<td></td>
        </tr>
    </tbody>
    <tfoot>
    	<tr>
        	<td></td>
        </tr>
    </tfoot>
</table>

```

### 表格属性

<table> border="1" width="500" height="400" cellspacing="0"

<thead><tbody> height   align valign "top bottom middle right left center"

### 跨行和跨列

rowspan 跨行 colspan跨列

### 常用标签

```  html
<br> 换行
<hr> 分割
<pre></pre>按原文显示
<iframe src="" width="" hight="" target=""></iframe>
    

```

### 表单

交互区域专门提供用户的数据

```html
<from method="post ,get" target= self action= >
	<input type= "text hidden submit">
    <textarea></textarea>
    <select>
        <option value="" selected></option>
    </select>
    <button type="submit reset button"></button>
        
    
</from>
```

disabled 禁用表单控件

lable 标签通过for和表单控件id匹配获取选中关联

fieldset 用于分组和legend分组标题

```html
<fieldset>
    <legend>标题</legend>
        
    
</fieldset>
```

### 字符实体

&nbsp: 空格 &lt;&gt;

# css层叠样式表

## 选择器

块级元素 行级元素 子元素 父元素 兄弟元素

<link rel="关系" href="文件地点"> 有的浏览器 rel必须加

```css

id
#a{
    
}
class
.a {
    
}
后代
div a b{
    
}
儿子
div>a{
    
}
交集
div.a{
    
}
并集
.beauty,
.dog,
.pig,
.rich{
    
}
下面的相邻的兄弟p元素
div+p{
    
}
所有div中的下面所有元素
div~p{
    
}
选择有title属性的元素
[title]{
    
}
属性选择器
选择有title的值为a的元素
[title="a"]{
    
}
选择有title的以字母a开头的元素
[title^="a"]{
    
}
选择有title的以字母a结束的元素
[title$="a"]{
    
}
选择有title的有a的元素
[title*="a"]{
    
}
选中的是没有访问过的a
a:link{
    
}
访问过
a:visited{
    
}
```

## 属性

边框 border：2px color style;

控制单元格间距 border-spacing: 0px;

控制列宽 table-layout fixed;

合并单元格的边框 border-collapse: collapse；间距失效

隐藏没有内容的单元格 empty-cells: hide; 生效的前提是不能合并边框

## 盒子模型

content内容区 padding内边距 border边框 margin外边距

border left right top bottom 4

color 左右上下 style 4

 左右上下color 4

border color width style 3

复合属性1 一共20个

padding-left right top bottom 顺时针

margin塌陷问题 

margin合并问题

## 其他属性

display 元素控制为块级还是行级元素

处理内容溢出 overflow 4 个属性值 还有-x -y两种方向

隐藏元素的两种方式 visibility show hidden 占位的 display none不占位

## 样式的继承

字体属性,文本属性,文字颜色等除了vertical-align 
不会继承的有边框,背景,外边距,宽高,溢出方式等。
不影响布局的都能继承。

## 有关布局的思考

```html
<template>

    <div class="nihao">
        <span class="inner">出来玩呀？</span>
        <div class="picture"></div>
    </div>

</template>
<style>


.nihao {
    /* 内容区的高 */

    height: 600px;
    width:600px;
    background-color: gray;
    text-align: center;
    line-height: 600px;
    font-size: 50px;
   
}
.inner{
    background-color: orange;
    vertical-align: middle;
    
}
.picture{
    width: 100px;
    height:100px;
    background-color: green;
    display: inline-block;
    vertical-align: middle;
}
</style>
```

通过文本属性来控制行内元素，利用vertical-align来绑定居中

## 元素之间的空白问题

回车会被解析成一个空白符

## 行内块的幽灵空白问题

字符的问题xg

给父元素设置vertical-align: middle;

给父元素设置font-size

## 浮动

lorem 10随机字符

文字环绕图片

float：left；

脱离文档流 而且被内容撑开 浮动了就是在上面铺了一层

浮动之后不会被当做文本处理（没有行内块空白问题） 

## 解除浮动    重点

伪元素清除浮动 

```css
parent::after{
	content:"";
	display:"";
	clear:"both";
}
```

## 相对定位

定位没有脱离文档流但是层级要比其他元素高 两个定位元素会有先后顺序

position： relative；

left right     听left               top  bottom  都有听top   绝对定位也一样

可以和margin和浮动使用不影响但是不推荐



## 绝对定位

position： absolute   脱离文档流且不会文字环绕

### 包含块概念

脱离文档流的元素，如果向外找祖先元素有定位则他就是包含块

没有脱离文档流的元素，父元素就是包含块

绝对定位也可以写负值

和margin一起用但是规则复杂不推荐

绝对定位会强过浮动，浮动失效 和固定定位一样。

### 定位元素

默认宽高被撑开，且能自由设置宽高

行内块 行内 块 三种元素复习一下

## 固定定位

脱离文档流

直接找视口 固定的广告视口 

写了什么就能用什么 

​     top               margin-top 就能和绝对定位一样。

开启固定定位的元素浮动失效

固定定位也会变为定位元素

## 黏性定位

不脱离文档流

参考离他最近的祖先元素拥有滚动条的。

overflow scroll; 空的滚动条里面没有滑块 

能和浮动一起用，margin也能用

和相对定位基本一致

## 定位的层级

定位层级高于普通元素，无论什么定位，显示层级都一样

如果定位的层级发生重叠，后面的元素会显示在前面的元素之上

可以通过z-index来调整

定位元素可能会被包含块连累

### 定位会越过padding          特殊应用

让定位元素充满包含块行left: 0 right: 0;  上下就也是0

让定位元素居中 上下左右全0 margin: auto;

第二个方案 让元素居中

left：50%

top：50%

margin-left ：负的宽度一半 

margin-top：负的高度一半

##  常用布局的单词

topbar 导航条

页头 header page-header

导航 nav navigator navbar

搜索框 search search-box

横幅 广告 宣传图 banner

主要内容 content main

侧边栏 aside sidebar

页脚 footer page-footer

## 重置默认样式

p元素有默认的上下margin

h1~h6也有默认margin 且字体加粗

body元素有默认的8px外边距margin

超链接有默认的颜色和下滑线

ul有默认的左内边距padding

