---
title: Nodejs开发问题小结
tag:
- Nodejs
categories:
- Nodejs
---
## 执行node app.js 监听80端口报错
```javascript
//app.js
const Koa = require('koa');
let app = new Koa();
...
app.listen(80);
```
<!-- more -->
```
events.js:160
      throw er; // Unhandled 'error' event
      ^

Error: listen EACCES 0.0.0.0:80
    at Object.exports._errnoException (util.js:1026:11)
    at exports._exceptionWithHostPort (util.js:1049:20)
    at Server._listen2 (net.js:1240:19)
    at listen (net.js:1289:10)
    at Server.listen (net.js:1385:5)
    at Application.listen (/Users/chang/future-team/ft-merchant-sso/node_modules/koa/lib/application.js:64:26)
    at Object.<anonymous> (/Users/chang/future-team/ft-merchant-sso/example/app.js:20:5)
    at Module._compile (module.js:556:32)
    at Object.Module._extensions..js (module.js:565:10)
    at Module.load (module.js:473:32)

```
**在Linux下，默认端口1024下的是要在root下才能使用，普通用户执行node app.js会报错**
解决办法是： `sudo node app.js`
