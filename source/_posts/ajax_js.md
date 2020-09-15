---
title: 原生ajax和jquery框架ajax的使用
date: 2018-10-02 16:49:42
categories: front #分类
tags: js
comments: true
---
# 原生
1. 创建一个异步调用对象，XMLHttpRequest
2. 创建一个新的HTTP请求，并且指定方法，url及参数等（XMLHttpRequest.open('GET/POST',url,true)）
3. 设置用于响应HTTP状态变化的函数（XMLHttpRequest.onreadystatuschange=function）
4. 发送HTTP请求（XMLHttpRequest.send()）
5. 获取异步调用数据
<!--more-->
GET:
//步骤一:创建异步对象
var ajax = new XMLHttpRequest();
//步骤二:设置请求的url参数,参数一是请求的类型,参数二是请求的url,可以带参数,动态的传递参数starName到服务端
ajax.open('get','getStar.php?starName='+name);
//步骤三:发送请求
ajax.send();
//步骤四:注册事件 onreadystatechange 状态改变就会调用
ajax.onreadystatechange = function () {
   if (ajax.readyState==4 &&ajax.status==200) {
    //步骤五 如果能够进到这个判断 说明 数据 完美的回来了,并且请求的页面是存在的
　　　　console.log(ajax.responseText);//输入相应的内容
  　　}
}
post请求:
//创建异步对象  
var xhr = new XMLHttpRequest();
//设置请求的类型及url
//post请求一定要添加请求头才行不然会报错
xhr.setRequestHeader("Content-type","application/x-www-form-urlencoded");
 xhr.open('post', '02.post.php' );
//发送请求
xhr.send('name=fox&age=18');
xhr.onreadystatechange = function () {
    // 这步为判断服务器是否正确响应
  if (xhr.readyState == 4 && xhr.status == 200) {
    console.log(xhr.responseText);
  } 
};

# Jquery
ajax({
  url: myUrl,
  type: 'get',
  dataType: 'json',
  timeout: 1000,
  success: function (data, status) {
    console.log(data)
  },
  fail: function (err, status) {
    console.log(err)
  }
})
不使用contentType: “application/json”则data可以是对象
$.ajax({
url: actionurl,
type: "POST",
datType: "JSON",
data: { id: nodeId },
async: false,
success: function () {}
});
使用contentType: “application/json”则data只能是json字符串
$.ajax({
url: actionurl,
type: "POST",
datType: "JSON",
contentType: "application/json"
data: "{'id': " + nodeId +"}",
async: false,
success: function () {}
});

JSON.parse()
JSON 通常用于与服务端交换数据。
在接收服务器数据时一般是字符串。
我们可以使用 JSON.parse() 方法将数据转换为json对象。
JSON.stringify()
JSON 通常用于与服务端交换数据。
在向服务器发送数据时一般是字符串。
我们可以使用 JSON.stringify() 方法将json对象转换为字符串。