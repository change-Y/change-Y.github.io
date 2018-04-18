---
title: Hexo功能配置
date: 2018-04-18 21:37:16
tags: hexo
categories: 搭建博客
---

### 一、更换主题
* 下载主题，[Next主题](https://github.com/theme-next/hexo-theme-next)
* 放入theme文件夹修改配置文件theme: Next
* 关闭目录自动展开：修改主题配置文件sidebar->disply: hide
* 选择主题风格：修改Schemes  

### 二、添加分类、标签、搜索等栏目
* **添加分类**：`hexo new page categories`，会在source目录下生成categories文件夹，修改配置文件:

```
---
title: 
date: 2017-05-02 22:27:12
type: "categories"
---
```

即添加`type: "categories"`参数

* **添加标签**：`hexo new page tags`
* **添加关于**：`hexo new page about`
* **添加搜索**：`npm install hexo-generator-search --save`，修改hexo配置文件，添加如下内容：

```
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```
修改主题配置文件：

```
local_search:
  enable: true
```

* 添加站点地图：`npm install hexo-generator-seo-friendly-sitemap --save`，修改hexo配置文件：

```
sitemap:
    path: sitemap.xml
```

修改主题配置文件：

```
 sitemap: /sitemap.xml || sitemap
```

* 添加RSS订阅：`npm install hexo-generator-feed --save`，修改主题配置文件`rss:  /atom.xml`