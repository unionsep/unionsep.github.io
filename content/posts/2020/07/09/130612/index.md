---
title: "Amazon Linuxの言語設定を変更した"
date: 2020-07-09T13:06:12+09:00
draft: false
toc: false
images:
tags: 
  - AWS
  - Amazon Linux
---

言語設定が英語のUTF-8になってたので、日本語設定しておいた。  
```shell
$ cat /etc/sysconfig/i18n
LANG=en_US.UTF-8
```

でもlocaleは日本語になってた。
```shell
$ locale
LANG=ja_JP.UTF-8
LC_CTYPE="ja_JP.UTF-8"
LC_NUMERIC="ja_JP.UTF-8"
LC_TIME="ja_JP.UTF-8"
LC_COLLATE="ja_JP.UTF-8"
LC_MONETARY="ja_JP.UTF-8"
LC_MESSAGES="ja_JP.UTF-8"
LC_PAPER="ja_JP.UTF-8"
LC_NAME="ja_JP.UTF-8"
LC_ADDRESS="ja_JP.UTF-8"
LC_TELEPHONE="ja_JP.UTF-8"
LC_MEASUREMENT="ja_JP.UTF-8"
LC_IDENTIFICATION="ja_JP.UTF-8"
LC_ALL=
```

まぁ、どっかの `rc` かなんかで設定してるんかなと推測。
```shell
$ echo $LANG
ja_JP.UTF-8
```

とりま変えとく
```shell
$ sudo vi /etc/sysconfig/i18n
#LANG=en_US.UTF-8
LANG=ja_JP.UTF-8
```
