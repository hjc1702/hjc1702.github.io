---
title: GitHub 代理
date: 2017-09-05 17:40:20
tags: [GitHub]
---

## 缘由

前几天突然不能通过 `ssh` 的方式访问 GitHub 的项目，查询各种资料后，通过配置 `ssh/config` 终于解决，记录一下。

## 依赖

两种代理方式其中一个

* http 代理
* socks5 代理

## 代码

编辑 `~/.ssh/config`, 添加一下代码

```bash
Host github.com
   Hostname ssh.github.com
   User git
   Port 443
   # 如果是 HTTP 代理，把下面这行取消注释，并把 proxyport 改成自己的 http 代理的端口
   # ProxyCommand socat - PROXY:127.0.0.1:%h:%p,proxyport=6667
   # 如果是 socks5 代理，则把下面这行取消注释，并把 1086 改成自己 socks5 代理的端口
   # ProxyCommand nc -v -x 127.0.0.1:1086 %h %p
```

## 参考资料

* [设置Git代理](https://imciel.com/2016/06/28/git-proxy/)
* [GitHub](https://help.github.com/articles/using-ssh-over-the-https-port/)