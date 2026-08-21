---
title: "あれ？iTerm2でコピーできんくなった"
date: 2020-07-09T13:22:21+09:00
draft: false
toc: false
images:
tags: 
  - vim
  - iTerm
---

いつの日か、iTerm上で文字列を選択しても、コピーできんくなった。。  
いや、あれ？このインスタンスだとできるけど、これだとできんなぁ。。みたいな感じだったので、まぁ、ついてないだけやろって思ってたけど、いやもうムリコレーってなったんで、雑にググったらこちらのページに行き着きました。

{{< cite url="http://gozuk16.hatenablog.com/entry/2017/01/11/092152" >}}

ありがとうございます、助かりました！  
なんかvi起動したときの、 `visual mode` でよく起きるわぁ〜とか思ってたらその通りでございました。  
重複になりますが、僕もウェブに残しておきます 🙇‍♂️

- Preferences > Profile > Terminal > Terminal Emulation
  - `Enable mouse reporting` のチェック外す