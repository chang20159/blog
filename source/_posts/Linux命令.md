---
title: 常用Linux命令
tag:
- Linux
categories:
- 工具
---


## 常用
查询端口占用情况
` lsof -i:端口号`
或
`netstat -apn|grep <端口号>`

结束占用端口的进程
`kill pid`

查看进程详细信息
`ps -aux | grep pid`

查看文件最后修改时间
`ls -al --full-time  `
BSD版本(MAC OSX)不支持--full-time选项，GNU版本的ls支持--full-time
若想在MAC OSX查看文件最后修改时间，可用
`ls -lT`

## DNS相关
查询 www.dpfile.com 的A记录，并返回简短的结果
```
changdeMacBook-Pro:sharing chang$ dig www.dpfile.com -t A +short
www.dpfile.com.fast.cdntip.com.
dpfile.fast.cdntip.com.
180.153.100.168
122.246.3.192
122.246.3.105
122.228.251.28
180.153.100.167
122.228.251.11
180.153.100.141
```

用 dig 命令查看从根域名到指定域名中间可能经过的所有域名服务器，使用 +trace
```
changdeMacBook-Pro:sharing chang$ dig www.dpfile.com +trace @8.8.8.8

; <<>> DiG 9.8.3-P1 <<>> www.dpfile.com +trace @8.8.8.8
;; global options: +cmd
.           155761  IN  NS  a.root-servers.net.
.           155761  IN  NS  b.root-servers.net.
.           155761  IN  NS  c.root-servers.net.
.           155761  IN  NS  d.root-servers.net.
.           155761  IN  NS  e.root-servers.net.
.           155761  IN  NS  f.root-servers.net.
.           155761  IN  NS  g.root-servers.net.
.           155761  IN  NS  h.root-servers.net.
.           155761  IN  NS  i.root-servers.net.
.           155761  IN  NS  j.root-servers.net.
.           155761  IN  NS  k.root-servers.net.
.           155761  IN  NS  l.root-servers.net.
.           155761  IN  NS  m.root-servers.net.
;; Received 228 bytes from 8.8.8.8#53(8.8.8.8) in 70 ms

com.            172800  IN  NS  l.gtld-servers.net.
com.            172800  IN  NS  k.gtld-servers.net.
com.            172800  IN  NS  e.gtld-servers.net.
com.            172800  IN  NS  g.gtld-servers.net.
com.            172800  IN  NS  m.gtld-servers.net.
com.            172800  IN  NS  j.gtld-servers.net.
com.            172800  IN  NS  a.gtld-servers.net.
com.            172800  IN  NS  c.gtld-servers.net.
com.            172800  IN  NS  b.gtld-servers.net.
com.            172800  IN  NS  h.gtld-servers.net.
com.            172800  IN  NS  d.gtld-servers.net.
com.            172800  IN  NS  f.gtld-servers.net.
com.            172800  IN  NS  i.gtld-servers.net.
;; Received 492 bytes from 202.12.27.33#53(202.12.27.33) in 162 ms

dpfile.com.     172800  IN  NS  ns3.dnsv4.com.
dpfile.com.     172800  IN  NS  ns4.dnsv4.com.
;; Received 362 bytes from 192.43.172.30#53(192.43.172.30) in 378 ms

www.dpfile.com.     600 IN  CNAME   www.dpfile.com.fast.cdntip.com.
dpfile.com.     86400   IN  NS  ns4.dnsv4.com.
dpfile.com.     86400   IN  NS  ns3.dnsv4.com.
;; Received 130 bytes from 125.39.213.169#53(125.39.213.169) in 45 ms
```

nslookup查看dns信息
```
changdeMacBook-Pro:sharing chang$ nslookup www.dpfile.com
Server:     10.128.16.28
Address:    10.128.16.28#53

Non-authoritative answer:
www.dpfile.com  canonical name = www.dpfile.com.fast.cdntip.com.
www.dpfile.com.fast.cdntip.com  canonical name = dpfile.fast.cdntip.com.
Name:   dpfile.fast.cdntip.com
Address: 180.153.100.141
Name:   dpfile.fast.cdntip.com
Address: 122.228.251.11
Name:   dpfile.fast.cdntip.com
Address: 122.228.251.28
Name:   dpfile.fast.cdntip.com
Address: 180.153.100.168
Name:   dpfile.fast.cdntip.com
Address: 122.246.3.192
Name:   dpfile.fast.cdntip.com
Address: 180.153.100.167
Name:   dpfile.fast.cdntip.com
Address: 122.246.3.105
```
<!-- more -->
## 文件和目录管理
- find
- rmdir
- which
- whereis
- tail
- mkdir
- chown
- chmod
- chgrp
- less
- head
- more
- touch
- stat
- rm
- colrm
- rename
- pwd
- ls
- mv
- cd
- cp
- cat
- ln
- dirname
- dd
- gzip
- zip
- unzip
- tar
- zcat
- diff
- tree
- grep
- dump

## 网络管理
- curl
- host
- instat
- ip
- wget
- ssh
- ping
- netstat
- ifconfig
- hostname

## 系统管理
### 系统安全
- syslog
- openssl
- last
- sudo

- killall
- ps
- pkill
- passwd
- finger
- logname
- su
- usermod
- shutdown

## 软件·打印·开发·工具
- date
- whoami
- users
- clear
- sleep
- man
- whatis
- cal
- bc
- who

## 硬件·内核·Shell·监测
- sh
- du
- dstat
- top
- free
- uptime
- vmstat
- tload
- env
- export
- uname
- kill
- type
- help
- history
- wait
- exit
- alias
- unalias
- echo
- df
- arch




