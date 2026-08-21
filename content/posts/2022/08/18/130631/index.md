---
title: "Debianの言語設定を変更した"
date: 2022-08-18T13:06:31+09:00
draft: false
toc: false
images:
tags: 
  - Debian
---

{{< cite url="https://unionsep.hatenablog.com/entry/2020/07/09/130307" >}}

前にDebianのLocal timeをJSTに変えたんですが、そういえば、言語設定が英語のままだな〜と思ってました。
Amazon Linuxで言語設定変えたけど、これ、言語設定と共にやっといてもええかな〜と思ったんで、メモします。

{{< cite url="https://unionsep.hatenablog.com/entry/2020/07/09/130612" >}}

OSは `Debian 10`
```shell
$ cat /etc/os-release
PRETTY_NAME="Debian GNU/Linux 10 (buster)"
NAME="Debian GNU/Linux"
VERSION_ID="10"
VERSION="10 (buster)"
VERSION_CODENAME=buster
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
```

こういう感じで英語な感じのレスポンス
```shell
$ ll
-bash: ll: command not found
```

日本語のパッケージがインストールされてなかったんで追加
```shell
$ sudo apt install task-japanese locales-all
```

ロケールを変更するには `localectl` コマンド
これで `/etc/default/locale` が `ja_JP.UTF-8` に変わった
```shell
$ sudo localectl set-locale LANG=ja_JP.UTF-8 LANGUAGE="ja_JP:ja"
```

変えた設定を適用する
```shell
$ source /etc/default/locale
```

日本語が返ってきたっす
```shell
$ ll
-bash: ll: コマンドが見つかりません
```