---
title: CSS实现垂直居中的几种方法
date: 2018-09-06 16:49:42
categories: front #分类
tags: css
comments: true
---
1. table-cell
html结构：
<div class="box box1">
        <span>垂直居中</span>
</div>
css：
.box1{
    display: table-cell;
    vertical-align: middle;
    text-align: center;        
}
<!--more-->
2. display:flex
.box2{
    display: flex;
    justify-content:center;
    align-items:center;
}
3. 绝对定位和负边距
.box3{position:relative;}
.box3 span{
            position: absolute;
            width:100px;
            height: 50px;
            top:50%;
            left:50%;
            margin-left:-50px;
            margin-top:-25px;
            text-align: center;
        }
4. 绝对定位和0
.box4 span{
  width: 50%; 
  height: 50%; 
  background: #000;
  overflow: auto; 
  margin: auto; 
  position: absolute; 
  top: 0; left: 0; bottom: 0; right: 0; 
}
