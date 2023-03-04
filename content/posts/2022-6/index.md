---
title: "Cloudflare使用问题"
date: 2022-03-05T11:00:00+08:00
draft: false
authors: ["zhyoch"]
tags: [Blog,Network]
featuredImagePreview: "/2022-6/featuredImagePreview.svg"
summary: 记录在使用Cloudflare时遇到的问题, 比如JS文件加载但不生效。
---

## JS文件加载了但不生效

本博客采用的评论系统是[Twikoo](https://github.com/imaegoo/twikoo), 有次出现了奇怪的问题: 

通过浏览器F12工具可以看到已经加载了`twikoo.all.min.js`文件, 云函数的数据库集合`counter`中也显示刚才浏览的文章的阅读数已增加, 但是文章下方并不显示评论框, 只有刷新网页后才显示。

我排查过很多方面, 甚至一度以为已经解决了问题, 但后来问题再次出现😨。最终, 我找到了原因——Cloudflare的JS异步加载设置。

### 解决方法

Cloudflare站点→速度→优化→禁用`Rocket Loader`

{{< image src="1.png" caption="禁用Rocket Loader" height="auto" width="80%">}}