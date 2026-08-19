---
title: "後でAnsibleにしたいsensu-serverのインストールログ"
date: 2016-06-20T17:26:29+09:00
draft: false
toc: true
images:
tags: 
  - ansible
---

sensu influxDB grafana環境を試してみたくて、とりあえずsensuとuchiwaをVagrantで実現してみた。
下記のページを写経させてもらった。  
SSL証明は0.25が最新ぽかったけど、latest指定でも大丈夫そう。  
実際、監視させてみて感触掴んだら、頑張ってAnsible化するっす。

{{< cite url="http://qiita.com/narvaux/items/15da8f71f7e175116d8b" >}}

{{< cite url="https://github.com/unionsep/install-log/tree/master/sensu" >}}
