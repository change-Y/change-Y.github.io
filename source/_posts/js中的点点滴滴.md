---
title: JavaScript中的点点滴滴
date: 2018-07-13 17:47:13
tags: JavaScript
categories: JavaScript
---
>这里记录一些js学习中遇到的问题及解决方案，也是对js知识的积累。

### 为DOM元素添加事件的一些问题
今天在学习ajax的时候，给页面的button按钮添加onclick事件时，发现了下面一些问题：

``` javascript
btn.onclick = request();
```
这样写会页面加载会直接执行request函数。

``` javascript
btn.onclick = request;
```
这样则不会直接执行，但面临的问题是如果需要传递参数怎么办？于是有了下面这种写法

``` javascript
btn.onclick = function(arg){
	request(arg);
}
```
所以在为元素添加事件，包括添加监听事件以及jQuery的写法中，如需传参，都要按照函数内调用函数这种写法才能保证事件能够正确绑定。

在元素的特性中的写法则为：

``` html
<button onclick="request()";>请求</button>
```

### 回调函数的理解
>在JavaScript中，回调函数具体的定义为：函数A作为参数(函数引用)传递到另一个函数B中，并且这个函数B执行函数A。我们就说函数A叫做回调函数。如果没有名称(函数表达式)，就叫做匿名回调函数。

例如forEach方法：

``` javascript
arr.forEach(function(value,index,arr){
	//这里就是回调函数
})
```