---
title: "Sitemap小踩坑"
date: 2022-01-12T22:27:30+08:00
url: "/2022/01/12/sitemap小踩坑/"
categories: ["2022寒假"]
tags: ["搭建踩坑"]
---
### sitemap小踩坑

昨天下午使用Google Console验证了网站。之后又向Google提交了sitemap，这里小踩一坑（其实大概是虚惊一场）。

在网上搜索“hexo+sitemap”关键字就可以找到很多相关的教程，流程基本上也都是一样的：

第一步先安装hexo-generator-sitemap，在Hexo目录下打开Git Bash，然后使用如下命令安装

```shell
npm install hexo-generator-sitemap --save
npm install hexo-generator-baidu-sitemap --save
```

即可安装sitemap的生成工具（第一行可以用于Google的站长工具，第二行是百度的，两个类似，因为我的博客用的是github page服务，所以就没有向百度提交），然后在根目录的_config.yml中找到url一项进行修改，改成自己的域名即可。

之后每一次使用部署命令：

```shell
hexo g
hexo d
```

都会自动生成站点地图文件sitemap.xml。在public文件夹里面就可以找到，部署的时候也能够同步上去。

接着做好在Google上的网站验证（具体方法网上也很多，主流的方案是部署一个.html的文件到代码仓库里面进行验证）。验证完成后我提交了sitemap，显示成果提交但立刻在status一栏出现“couldn't fetch”红色字样。点进去详情没有报错，只是显示“sitemap could not be read”。几乎是本能地把这种提示和各种oj的报错联系起来——那么肯定是我哪里写错了。

结果在百度上兜了好几圈，没有找到什么有用的解答信息，因为一般来说这种生成器也不会出现什么格式错误。

最后还是在Google上找到了解答，先是看到了下面这篇回答：https://support.google.com/webmasters/thread/12746546/error-sitemap-could-not-be-read-couldn-t-fetch?hl=en

虽然问题不同，但是解答的老哥非常耐心地说了“一般而言都是处于pending状态”。我当时看到寻思是会显示“Pending”的提示字样而不是出现一个红色的字体。又看了几篇文章，没有什么头绪，正准备在自己投递问题的时候看到了另一篇文章：https://www.jcchouinard.com/sitemap-could-not-be-read-couldnt-fetch-in-google-search-console/

直接命中问题所在，按照文中所述方式进行访问验证，谷歌爬虫确实可以访问到我的sitemap，而我也没有写错任何东西。说明接下来的事情就是交给时间和谷歌了。应该过段时间就能在谷歌上搜到博客了。

