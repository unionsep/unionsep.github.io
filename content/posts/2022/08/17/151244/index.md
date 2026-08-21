---
title: "Ubuntu立てるときに、はじめにやることの大体のアレ"
date: 2022-08-17T15:12:44+09:00
draft: false
toc: false
images:
tags: 
  - AWS
  - Ubuntu
---

AWSでもGCPでもどんなクラウドでも、もしくはクライアントPCでUbuntuをインストールしてなんかするときに、ワイが大体やることをまとめたかったのでメモ書き。
いつも大体やってるんだけど、ほとんどあのコマンドなんやっけ？とかなって調べて「あぁそうやったわ」ってなるのがいい加減面倒くさくなったので。。

- **OSは以下**  
今回は訳合って `Ubuntu 20.04` 。
書いた時点で最新のLTSである `22.04` が良かったんだけど、MongoDBがうまく対応してなかったかなんかで一個前のLTSを採用した。
```shell
$ cat /etc/os-release
NAME="Ubuntu"
VERSION="20.04.4 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04.4 LTS"
VERSION_ID="20.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=focal
UBUNTU_CODENAME=focal
```

- **rootになる**  
初期設定はroot作業が多いので、面倒くさいからrootで作業してる。
sudoでやったほうがいいけど、まぁ、これだけだしそんなにセキュリティがちがちじゃなくてもいいかな。
```shell
$ sudo -s
```

- **aptのアップグレードとアップデート**  
AWSでUbuntu作ったら `source.list` のリポジトリが `ap-northeast-1` のミラーを見てるみたいなで、それをそのまま利用させてもらう。
クライアントPCとかでデスクトップとして使うとかの用途なら、どこかの大学とかが公開してるミラーサイトに変更する。
```shell
# apt upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following NEW packages will be installed:
  linux-aws-5.15-headers-5.15.0-1017 linux-headers-5.15.0-1017-aws linux-image-5.15.0-1017-aws linux-modules-5.15.0-1017-aws
...
# apt update
Hit:1 http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu focal InRelease
...
```

- **時間合わせる**  
過去に記事にしたけど、とっちらかっるのが嫌なのでリンクと内容書いとく
{{< cite url="https://unionsep.hatenablog.com/entry/2020/07/09/130307" >}}

```shell
# timedatectl
               Local time: Wed 2022-08-17 02:39:47 UTC
           Universal time: Wed 2022-08-17 02:39:47 UTC
                 RTC time: Wed 2022-08-17 02:39:46
                Time zone: Etc/UTC (UTC, +0000)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
# timedatectl list-timezones|grep Tokyo
Asia/Tokyo
# timedatectl set-timezone Asia/Tokyo
# date
Wed Aug 17 11:40:38 JST 2022
```

- **日本語にしたいな**  
英語（ `us` ）のままでも別にいいんだけど、マニュアル（ `man` ）は日本語のほうが緊急時に助かったりするので、日本語にする。
```shell
# localectl status
   System Locale: LANG=C.UTF-8
       VC Keymap: n/a
      X11 Layout: us
       X11 Model: pc105
# localectl list-locales
C.UTF-8
en_US.UTF-8
# apt install language-pack-ja -y
Reading package lists... Done
Building dependency tree
Reading state information... Done
...
# localectl list-locales
C.UTF-8
en_US.UTF-8
ja_JP.UTF-8
# localectl set-locale LANG=ja_JP.UTF-8
```

- **ホスト名変えたいな**  
AWSだとプライベートIPを含む名前になるみたいなんだけど、管理サーバが多くなってきて脳みその切り替えのコストがしんどくなってきたんでわかり易い名前に変更してます。
```shell
# hostnamectl
   Static hostname: ip-xxx-xxx-xxx-xxx
         Icon name: computer-vm
           Chassis: vm
        Machine ID: aaaaa
           Boot ID: bbbbb
    Virtualization: kvm
  Operating System: Ubuntu 20.04.4 LTS
            Kernel: Linux 5.13.0-1029-aws
      Architecture: x86-64
# hostnamectl set-hostname XXXXX
```

- **ここで一旦再起動**  
ロケール変えたりホスト名変えたけど、リブートしないと反映されないっぽいので、一旦このタイミングで再起動してる。
```shell
# reboot
```

- **マニュアルを日本語にする**  
ゆっくり英語のマニュアルを読むのはいいんだけど、急いでるときはちょっとしんどいんで `man` は日本語にしておく
```shell
# apt install manpages-ja
パッケージリストを読み込んでいます... 完了
依存関係ツリーを作成しています
状態情報を読み取っています... 完了
以下のパッケージが新たにインストールされます:
  manpages-ja
アップグレード: 0 個、新規インストール: 1 個、削除: 0 個、保留: 0 個。
...
# apt install manpages-ja-dev
パッケージリストを読み込んでいます... 完了
依存関係ツリーを作成しています
状態情報を読み取っています... 完了
以下のパッケージが新たにインストールされます:
  manpages-ja-dev
アップグレード: 0 個、新規インストール: 1 個、削除: 0 個、保留: 0 個。
...
```

- **visudoのエディタを変える**  
visudoのエディタが `nano` であんまり慣れてないから `vim.tiny` に変える。
選択肢の4番を指定する。
```shell
# update-alternatives --config editor
There are 4 choices for the alternative editor (providing /usr/bin/editor).

  Selection    Path                Priority   Status
------------------------------------------------------------
* 0            /bin/nano            40        auto mode
  1            /bin/ed             -100       manual mode
  2            /bin/nano            40        manual mode
  3            /usr/bin/vim.basic   30        manual mode
  4            /usr/bin/vim.tiny    15        manual mode
```

- **ユーザを追加**  
ユーザ作ってから、 `visudo` を使ってパスワードなしで `sudo` できる権限をつける。
```shell
# adduser unionsep --disabled-password
Adding user `unionsep' ...
Adding new group `unionsep' (1001) ...
Adding new user `unionsep' (1001) with group `unionsep' ...
Creating home directory `/home/unionsep' ...
Copying files from `/etc/skel' ...
Changing the user information for unionsep
Enter the new value, or press ENTER for the default
	Full Name []:
	Room Number []:
	Work Phone []:
	Home Phone []:
	Other []:
Is the information correct? [Y/n] Y
```

- **追加したユーザに `sudo` できるようにする**  
`visudo` で開いた `/etc/sudoers.tmp` に以下追加
```shell
# visudo
unionsep ALL=NOPASSWD: ALL
```

- **作ったユーザがkeypairでログインできるようにしてる**  
AWSでは、`ubuntu` というデフォルトのユーザが作成され、インスタンス生成時にこのユーザに独自の `keypair` を設定してる。
各々のユーザに `keypair` を作って各々のインスタンスに配布したいのだけど、Bastionに入れるユーザがワイしかいないので、コピーしてる・・・
```shell
# cp -r /home/ubuntu/.ssh /home/unionsep/
# chown -R unionsep:unionsep /home/unionsep/.ssh
```

---

その他、 `ll` コマンドなんかの `alias` や、 `LSCOLORS` で `ls` コマンドの色つけたりするけど、Ubuntuはすでにできてるのでやらない。
作ったインスタンスはAMIとかにしておくんだけど、AMIって課金対象だったりするので、あんまり作ってないなぁ。
Ansibleで構成管理したりしたらコマンド一発だしね。。  

こんな感じで作ったインスタンスに `node` やら `java` やら `elasticsearch` やらなんやかんや入れて構築したりしてる感じ。

では〜