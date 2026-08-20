---
title: "Bitnamiの右ロゴリンクを無効にした"
date: 2020-05-28T17:14:01+09:00
draft: false
toc: false
images:
tags: 
  - AWS
  - bitnami
---

Bitnami Redmineでもほぼ同じだった。

1. sshでログイン
2. コマンド実行
```shell
$ sudo /opt/bitnami/apps/wordpress/bnconfig --disable_banner 1
```
3. httpdリスタート
```shell
$ sudo /opt/bitnami/ctlscript.sh restart apache
Unmonitored apache
Syntax OK
/opt/bitnami/apache2/scripts/ctl.sh : httpd stopped
Syntax OK
/opt/bitnami/apache2/scripts/ctl.sh : httpd started at port 80
Monitored apache
```