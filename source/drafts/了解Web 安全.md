---
title: 了解Web安全
categories:
- 网络
---

前端开发需要了解一些web安全方面的知识，收集了一些资料学习一下。特别感谢[Go web编程](https://github.com/astaxie/build-web-application-with-golang)这本书，虽然是主要是写Go语言编程的，但是书中对web 安全方面的知识讲得特别好，这里也摘录了很多。

<!--more-->
	
网站常见攻击手段: 

- CSRF
- XSS
- SQL注入
	
安全策略：

- 同源策略
- 数据过滤
- HTTPS
- 安全加密

## 网站常见攻击手段

### CSRF

CSRF（Cross-site request forgery），中文名称：跨站请求伪造，也被称为：one click attack/session riding，缩写为：CSRF/XSRF。

那么CSRF到底能够干嘛呢？你可以这样简单的理解：攻击者可以盗用你的登陆信息，以你的身份模拟发送各种请求。攻击者只要借助少许的社会工程学的诡计，例如通过QQ等聊天软件发送的链接(有些还伪装成短域名，用户无法分辨)，攻击者就能迫使Web应用的用户去执行攻击者预设的操作。例如，当用户登录网络银行去查看其存款余额，在他没有退出时，就点击了一个QQ好友发来的链接，那么该用户银行帐户中的资金就有可能被转移到攻击者指定的帐户中。

所以遇到CSRF攻击时，将对终端用户的数据和操作指令构成严重的威胁；当受攻击的终端用户具有管理员帐户的时候，CSRF攻击将危及整个Web应用程序。


#### 更多

- [CSRF 攻击的应对之道](https://www.ibm.com/developerworks/cn/web/1102_niugang_csrf/)
- [预防CSRF攻击](https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/09.1.md)



### XSS攻击

随着互联网技术的发展，现在的Web应用都含有大量的动态内容以提高用户体验。所谓动态内容，就是应用程序能够根据用户环境和用户请求，输出相应的内容。动态站点会受到一种名为“跨站脚本攻击”（Cross Site Scripting, 安全专家们通常将其缩写成 XSS）的威胁，而静态站点则完全不受其影响。

#### 什么是XSS

XSS攻击：跨站脚本攻击(Cross-Site Scripting)，为了不和层叠样式表(Cascading Style Sheets, CSS)的缩写混淆，故将跨站脚本攻击缩写为XSS。

XSS是一种常见的web安全漏洞，它允许攻击者将恶意代码植入到提供给其它用户使用的页面中。XSS通常可以分为两大类：

- 存储型XSS
	主要出现在让用户输入数据，供其他浏览此页的用户进行查看的地方，包括留言、评论、博客日志和各类表单等。应用程序从数据库中查询数据，在页面中显示出来，攻击者在相关页面输入恶意的脚本数据后，用户浏览此类页面时就可能受到攻击。这个流程简单可以描述为:恶意用户的Html输入Web程序->进入数据库->Web程序->用户浏览器。
	
- 反射型XSS

	主要做法是将脚本代码加入URL地址的请求参数里，请求参数进入程序后在页面直接输出，用户点击类似的恶意链接就可能受到攻击。

#### 更多

- [避免XSS攻击](https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/09.3.md)

- [MDN: 跨站脚本攻击](https://developer.mozilla.org/zh-CN/docs/Glossary/Cross-site_scripting)
- [Web 安全编程实战](https://www.ibm.com/developerworks/cn/web/1012_weiqiang_webattack/)
- [新浪微博遭受XSS跨站脚本攻击实例分析](http://www.rising.com.cn/newsletter/news/2011-08-18/9621.html)


### 避免SQL注入

## 安全策略

### 同源策略

同源策略限制一个源的文档或脚本与另一个源的资源进行交互，以保证用户信息安全。同源需要协议、域名和端口完全相同。
但实际开发中，我们希望能够进行寡欲请求。这里介绍了几种跨域方案：

[Web开发中跨域的几种解决方案](http://harttle.com/2015/10/10/cross-origin.html?utm_source=tuicool&utm_medium=referral)

注意： **跨域不一定是浏览器限制了发起跨站请求，而也可能是跨站请求可以正常发起，但是返回结果被浏览器拦截了。最好的例子是 CSRF 跨站攻击原理，请求是发送到了后端服务器无论是否跨域！注意：有些浏览器不允许从 HTTPS 的域跨域访问 HTTP，比如  Chrome 和 Firefox，这些浏览器在请求还未发出的时候就会拦截请求，这是一个特例。）**

#### 更多

- [浏览器的同源策略](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)
- [HTTP访问控制（CORS）](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)
- [Server-Side Access Control](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Server-Side_Access_Control)
- [window.postMessage](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/postMessage)

### 数据过滤

数据过滤在Web安全中起到一个基石的作用，大多数的安全问题都是由于没有过滤数据和验证数据引起的，例如CSRF攻击、XSS攻击、SQL注入等都是没有认真地过滤数据引起的。

#### 更多

- [确保输入过滤](https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/09.2.md)



### HTTPS
### 安全加密
