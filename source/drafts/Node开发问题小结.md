---
title: Nodejs开发问题小结
tag:
- Nodejs
categories:
- 工作
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

## node升级后执行npm报错 Cannot find module 'internal/fs'
```
npm WARN package.json apollo-shop-map-static@0.1.0 No README data
npm ERR! Darwin 16.3.0
npm ERR! argv "/usr/local/bin/node" "/usr/local/lib/node_modules/cnpm/node_modules/.bin/npm" "--userconfig=/Users/chang/.cnpmrc" "--disturl=https://npm.taobao.org/mirrors/node" "--cache=/Users/chang/.cnpm" "--registry=https://registry.npm.taobao.org" "--node-gyp=/usr/local/lib/node_modules/cnpm/node_modules/node-gyp/bin/node-gyp.js" "install" "eagle-ui" "--save"
npm ERR! node v7.10.0
npm ERR! npm  v2.14.11
npm ERR! code MODULE_NOT_FOUND

npm ERR! Cannot find module 'internal/fs'
npm ERR! 
npm ERR! If you need help, you may report this error at:
npm ERR!     <https://github.com/npm/npm/issues>

npm ERR! Please include the following file with any support request:
npm ERR!     /Users/chang/merchant/frontend/apollo-shop-map-static/npm-debug.log
```
**解决办法：**
- rm -rf /usr/local/lib/node_modules/npm 
- 安装nodejs http://nodejs.cn/download/

```
>node -v
v8.0.0
>npm -v
5.0.0
```
