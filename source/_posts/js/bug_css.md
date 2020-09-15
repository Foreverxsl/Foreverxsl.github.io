---
title: 常见浏览器兼容性问题与解决方案
date: 2018-10-11 16:01:42
categories: front #分类
tags: css
comments: true
---
1. 不同浏览器的标签默认的margin和padding不同：
问题症状：随便写几个标签，不加样式控制的情况下，各自的margin 和padding差异较大。
可以使用Normalize来清除默认样式，也可以使用如下代码：
body,h1,h2,h3,ul,li,input,div,span,a,form …… { margin:0; padding:0; }
2. 浏览器兼容问题二：块属性标签float后，又有横行的margin情况下，在IE6显示margin比设置的大：
问题症状:常见症状是IE6中后面的一块被顶到下一行
解决方案：在float的标签样式控制中加入 display:inline;将其转化为行内属性 
3. 图片默认有间距
问题症状：几个img标签放在一起的时候，有些浏览器会有默认的间距，通配符清除间距也不起作用。
解决方案：使用float属性为img布局