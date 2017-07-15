---
title: 给Hexo+Github page博客绑定域名的一些问题
tag:
- Hexo
- GitHub
categories:
- 静态博客
---

项目终于做完了，忽然想到买了很久的域名一直没有用，本来以为还要备案啥的，就一直没倒腾。
今天研究了下域名绑定，居然很快就弄好了。但是也遇到了几个问题，还是记录一下吧~

-  升级Node.js后，执行hexo generate报错
-  Hexo博客绑定域名 CNAME文件问题
-  个人主页添加域名后项目主页访问不了了

<!-- more -->
### 升级Node.js后，执行hexo generate报错

```
Error: The module '/usr/local/lib/node_modules/hexo-cli/node_modules/dtrace-provider/build/Release/DTraceProviderBindings.node'
was compiled against a different Node.js version using
NODE_MODULE_VERSION 48. This version of Node.js requires
NODE_MODULE_VERSION 51. Please try re-compiling or re-installing
the module (for instance, using npm rebuild ornpm install).
```

重新安装 hexo-cli

```
npm install -g hexo-cli
```
或者

```
cnpm install -g hexo-cli
```

如果提示没有权限，再加上sudo。
然后删除node_modules 再重新 npm install 国内hexo好像都得cnpm

### Hexo博客绑定域名

使用的github page，绑定域名过程如下：

**1、在阿里云购买域名**

[戳链接](https://wanwang.aliyun.com/domain/)   恩，chang20159.com 这个域名我买下了

**2、准备好github项目的机器IP**

终端执行    `ping chang20159.github.io`
  
```
>ping chang20159.github.io
PING github.map.fastly.net (151.101.72.133): 56 data bytes
64 bytes from 151.101.72.133: icmp_seq=0 ttl=50 time=90.449 ms
64 bytes from 151.101.72.133: icmp_seq=1 ttl=50 time=91.037 ms
```
  
  拿到github项目的机器IP   151.101.72.133

**3、绑定域名**

  要把域名chang20159.com 与 github page项目 chang20159.github.io绑定
 
  域名买好了之后，在控制台找到域名的入口，就可以看到这个，添加了主机记录@和www
  这样我们就可以访问 chang20159.com 和 www.chang20159.com 了
  ![](/images/domain/domain1.png)
  点击域名>域名解析>新手引导设置
 ![](/images/domain/domain2.png)
  按步骤绑定，绑定好了就能看到下面这个
 ![](/images/domain/domain3.png)
  
  现在还不能访问chang20159.com，还有一步
**4、 添加CNAME文件**
在chang20159.github.io项目中添加CNAME文件，文件没有后缀名，文件内容是：

```
chang20159.com
```
CNAME文件要添加到hexo项目的source目录下，在github项目的setting里面有个Custom domain选项，如果在那里添加cname也可以，但是hexo项目不行，因为如果source目录下没有cname文件的话每次deploy后那个设置又没有了。

### 个人主页添加域名后项目主页访问不了了
github上还有个项目sharing，本来没有cname，通过chang20159.github.io/sharing可以直接访问这个项目的，但是个人主页项目cname后项目主页就404了。

因为 访问 chang20159.github.io/sharing 会跳转到 chang20159.com/sharing ,但是chang20159.github.io项目是没有sharing文件的，所以就404了。

问题有了，办法也是有的。

**1、 在sharing这个项目中也添加CNAME文件**
文件内容是：

```
sharing.chang20159.com
```

**2、 在阿里云域名管理页面chang20159.com下添加一个记录**
就是下面这样：
这样就可以通过sharing.chang20159.com 访问 sharing项目的内容了。
 ![](/images/domain/domain4.png)

这样 访问sharing.chang20159.com 就能与机器151.101.72.133连接，然后通过cname找到sharing项目了





  