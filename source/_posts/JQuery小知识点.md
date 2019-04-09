---
title: JQuery小知识点
date: 2018-07-28 10:32:20
tags: JQuery
categories: JQuery
---
### 廖雪峰部分
#### 选择器
- 常用基础选择器
	- `ID:$("#id")`
	- `Class:$("class")`
	- `Tag:$("tag")`
	- `Attribute:$("[name='name']")`
- JQ选择器返回的是一个对象，这个对象类似于数组，如果没有查询到指定元素，那么返回[]，不会返回undefined和null。
- 若果有一个已存在的DOM对象，那么可以通过`$(aDomObject)`来将其转换为JQuery对象。
- 查找有多个class的节点时，`$(".class1.class2")`,中间没有空格。同样组合查找时多个选择器之间也没有空格，如：`$("input[name='email']")`,选出input下的name为email的元素。
- 多项选择时用逗号隔开，如：`$("div,input")`选出div和input标签。
- 层级选择器用空格隔开，如：`$("div.red input")`,选择class为red的div下的所有input。
- 子选择器用大于号隔开，如：`$("div#parent>div")`,选择id为parent的div下的直属div子元素。
- 过滤器用冒号隔开，如：
	- `$("ul#parent li:first-child")`,选择id为parent的ul下的第一个li;
	- `$("ul#parent li:last-child")`,选择id为parent的ul下的最后一个li;
	- `$("ul#parent li:nth-child(n/even/odd)")`,选择id为parent的ul下的第n个li/偶数li/奇数li;
- 表单相关：
	- `:input` 可以选择`<input>`，`<textarea>`，`<select>`和`<button>`
	- `:radio`：可以选择单选框，和input[type=radio]一样
	- `:focus`：可以选择当前输入焦点的元素
	- `:checked`：选择当前勾上的单选框和复选框
- 其他：
	- `:visaible`：选择可见的元素
	- `:hidden`：选择隐藏的元素
- 查找和过滤（通常对已经拿到的jq对象进行操作）
	- 向下查找：`$("#aDiv").find(".calss1")`
	- 选取父级：`$("selector").parent()`
	- 选取同级：`$("selector").next`,`$("selector").prev`
	- 过滤：jqObj.filter("selector"),注意返回的是符合过滤器中条件的元素，filter还可以接收过滤函数，这个函数返回符合条件的元素，如：
``` javascript
jqObj.filter(function(){
    return this.innerHTML.indexof("s") === 0
})
```
	筛选出文本内容为"s"开头的jqObj中的元素。
	**注意：这里的this是原生dom对象，所以要想使用jq的方法则需通过$(this)来将其转换为jq对象。**