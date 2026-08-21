---
title: "Amazon LinuxのタイムゾーンをUTCからJSTに変える"
date: 2020-07-09T13:08:58+09:00
draft: false
toc: false
images:
tags: 
  - AWS
  - Amazon Linux
---

Amazon Linux（2ではない）を選択してインスタンスを立てたらタイムゾーンがUTCだったのでJSTに変更しました。

- OSを確認  
CentOSってイメージだったけど、 `rhel fedora` ってなってるってことは、必ずしもCentOSと一緒ってことではなさそうなんすね。
```shell
$ cat /etc/os-release
NAME="Amazon Linux AMI"
VERSION="2018.03"
ID="amzn"
ID_LIKE="rhel fedora"
VERSION_ID="2018.03"
PRETTY_NAME="Amazon Linux AMI 2018.03"
ANSI_COLOR="0;33"
CPE_NAME="cpe:/o:amazon:linux:2018.03:ga"
HOME_URL="http://aws.amazon.com/amazon-linux-ami/"
```

- 時間を確認  
うん、9時間ずれてる。
```shell
$ date
2020年  7月  8日 水曜日 02:23:56 UTC
```

- `/etc/sysconfig/clock` を修正
```shell
$ sudo vi /etc/sysconfig/clock
#ZONE="UTC"
ZONE="Asia/Tokyo"
UTC=true
```

- シンボリックリンクの変更
```shell
sudo ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
```

- 時間を確認  
9時間戻った。
```shell
$ date
2020年  7月  8日 水曜日 11:41:30 JST
```

- crondを再起動  
Amazon Linux（2ではない）は、 `systemctl` が入ってなかったので、 `service` コマンドで再起動すれば良さそう。
```shell
$ sudo service crond restart
Stopping crond:                                            [  OK  ]
Starting crond:                                            [  OK  ]
```