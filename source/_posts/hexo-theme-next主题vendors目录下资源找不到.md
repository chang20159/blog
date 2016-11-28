---
title: NexT主题中vendors目录下资源找不到报404
tag:
- Hexo
- Jekyll
categories:
- 静态博客
---

昨天发了一篇新文章，部署之后博客都打不开了，两个站点控制台都抛红,NexT主题的source目录下有一个vendors目录，这个目录下的资源文件都无法加载，这是因为github和coding都升级了Jekyll的版本，新版本会忽略vendors文件，github在[What's new in GitHub Pages with Jekyll 3.3](https://github.com/blog/2277-what-s-new-in-github-pages-with-jekyll-3-3)中给了解释和解决办法。

 to make it easier to vendor third-party dependencies via package managers     like Bundler or NPM (or Yarn), Jekyll now ignores the vendor and node_modules directories by default, speeding up build times and avoiding potential errors. If you need those directories included in your site, set exclude: [] in your site's configuration file.

<!-- more -->
![](/images/vendors404/QQ20161110-1@2x.png)
这里有几个解决办法

- 把vendors全部手动替换成lib
- 重新clone主题，NeXT的作者已经修复了这个问题，但是重新clone需要重新设置
- 项目根目录下新建一个 .nojekyll 隐藏文件 ，内容为!vendor/*

参考：
- [What's new in GitHub Pages with Jekyll 3.3](https://github.com/blog/2277-what-s-new-in-github-pages-with-jekyll-3-3)
- [关于vendors文件夹里面的js文件和css文件404](https://github.com/hexojs/hexo/issues/2238)
- [Hexo本地预览没问题 deploy后主页空白](https://github.com/iissnan/hexo-theme-next/issues/1214)

