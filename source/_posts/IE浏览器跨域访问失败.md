---
title: IE浏览器跨域访问失败
tag:
- CORS
- 兼容性问题
- IE
categories:
- 工作
---
PC前端项目放在h5.dianping.com域名下， 后端项目在e.dianping.com下。    
chrome浏览器是可以正常访问的，IE浏览器访问页面获取不到数据。
在IE7以下版本的IE浏览器中使用ActiveXObject发送Ajax请求，IE7以上版本使用XMLHttpRequest发送Ajax请求，在IE8和IE9可以使用XDomainRequest进行跨域请求，IE10及以上使用支持跨域的XMLHttpRequest。
这次问题的产生来源于根据不同浏览器获得XMLHttpRequest对象逻辑有问题。
<!-- more -->
### 跨域访问没有权限
![](/images/IECORS/QQ20161224-1.png)
在ajax请求调用send方法时出错，提示没有权限。
因为IE浏览器默认对跨域访问有限制。需要在浏览器设置中去除限制。
- 设置 > Internet选项 > 安全 > 自定义级别 > 在设置中找到其他
- 在【其他】中将【通过域访问数据源】启用

### ActiveXObject没有withCredentials属性
解决了这个问题之后，依然获取不到数据，提示对象没有此属性，在调用send方法时出错。
![](/images/IECORS/QQ20161224-0.png)
查阅依赖fetch文件源码发现问题。

```javascript
    getXMLHttpRequest() {
        var xhr = null;
        try {
            xhr = new ActiveXObject("microsoft.xmlhttp");
        } catch (e1) {
            try {
                //非IE浏览器
                xhr = new XMLHttpRequest();
            } catch (e2) {
                window.alert("您的浏览器不支持ajax，请更换！");
            }
        }
        return xhr;
    };
    send(url,opts={}){
        ...
        let xhr = this.getXMLHttpRequest();
        if (opts.credentials === 'include') {
            xhr.withCredentials = true;
        }
```

代码中先new ActiveXObject,如果出现异常再new XMLHttpRequest。
这样做会有问题，IE浏览器中new ActiveXObject并不会出现异常，new ActiveXObject("microsoft.xmlhttp")的结果是一个空的对象。这是在IE11上的运行结果：
![](/images/IECORS/QQ20161224-2.png)
![](/images/IECORS/QQ20161224-3.png)
所以在后续设置withCreditial字段支持cookie跨域访问时，会提示该对象没有此属性。

在IE10及后续的版本都已支持包含CORS的XMLHttpRequest，如果需要跨域访问cookie,还需要升级到IE10或以上版本。

因为要跨域访问cookie, IE用户都需要升级到IE10或此版本以上。并且代码需要修改，通过判断window.XMLHttpRequest是否存在再去new实例，并且对象具有withCredentials属性才赋值。

```javascript
    getXMLHttpRequest() {
        var xhr;
        try {
                if(window.XMLHttpRequest){
                    xhr = new XMLHttpRequest();
                }else(window.ActiveXObject){
                    //IE浏览器低版本
                    xhr = new ActiveXObject("microsoft.xmlhttp");
                }
            } catch (e) {
                console.log(e);
                throw new ReferenceError("XMLHttpRequest is not supported")
            }
        return xhr;
    };

    send(url,opts={}){
        ...
        let xhr = this.getXMLHttpRequest();
        if (opts.credentials === 'include' && xhr.withCredentials) {
            xhr.withCredentials = true;
        }
```
如此，问题解决了~~
### 浏览器对XMLHttpRequest的兼容性
![](/images/IECORS/QQ20161224-0%402x.png)
### 参考
- [AJAX](https://developer.mozilla.org/zh-CN/docs/AJAX/Getting_Started)
- [ajax相关完整知识](https://segmentfault.com/a/1190000005830066#articleHeader16)
- [XDomainRequest](https://developer.mozilla.org/zh-CN/docs/Web/API/XDomainRequest#.E5.B1.9E.E6.80.A7)
- [HTTP访问控制(CORS)](https://developer.mozilla.org/zh-CN/docs/Web/API/XDomainRequest)



