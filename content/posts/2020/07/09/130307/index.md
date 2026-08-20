---
title: "DebianのLocal timeをJSTに変更した"
date: 2020-07-09T13:03:07+09:00
draft: false
toc: false
images:
tags: 
  - AWS
  - Ubuntu
  - Debian
---

EC2でDebian/Ubuntuベースのインスタンスを立ち上げたときに、だいたい `UTC` になってて9時間ほど前の時間になってると思う。  
それはちょっと面倒なので、 `JST` に変更します。  

- **Debian 10** でした。
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

- timezoneの確認
```shell
$ timedatectl
               Local time: Thu 2020-07-09 03:56:56 UTC
           Universal time: Thu 2020-07-09 03:56:56 UTC
                 RTC time: Thu 2020-07-09 03:56:57
                Time zone: Etc/UTC (UTC, +0000)
System clock synchronized: yes
              NTP service: inactive
          RTC in local TZ: no
```

- 東京のtimezoneの値を確認

```shell
$ timedatectl list-timezones|grep Tokyo
Asia/Tokyo
```

- timezoneを東京に変更

```shell
$ sudo timedatectl set-timezone Asia/Tokyo
```

- 東京の時間になったか確認

```shell
$ timedatectl
               Local time: Thu 2020-07-09 13:02:27 JST
           Universal time: Thu 2020-07-09 04:02:27 UTC
                 RTC time: Thu 2020-07-09 04:02:28
                Time zone: Asia/Tokyo (JST, +0900)
System clock synchronized: yes
              NTP service: inactive
          RTC in local TZ: no
```

---

インスタンスを再起動しても、Local timeが `JST` のままだったので、とりあえずよしとしました。