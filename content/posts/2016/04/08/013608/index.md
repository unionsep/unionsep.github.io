---
title: "Mackerelで遊んでみた"
date: 2016-04-08T01:36:08+09:00
draft: false
toc: true
images:
tags: 
  - mackerel
---

はてなさんが展開してるMackerelを使う機会に恵まれたので、ついでに監視してみたかったものを利用してMackerelを一通り触ってみました。  
meetupで出会った方に仲良くしていただいたので、導入してみたよっ！ってお伝えしたら、ブログも勧められたので、書いたことないブログも書いてみました。
Markdown記法に慣れてないので、GitのWiki書くのもやっぱり苦手で、克服できたらええなぁ...  

1. [Mackerelにサインアップ、agentをインストールしてみる](#no1)
1. [Hubotがちょいちょい落ちてたので、独自監視項目を追加してみる](#no2)
1. [監視ルールを設定してみる](#no3)
1. [Slackに投稿できるようにしてみる](#no4)
1. [Slackから再起動スクリプトを叩けるようにできたらええな...](#no5)
1. [まとめなど](#no6)

#### サインアップとインストール {#no1}
なんのこっちゃないです。[ここ](https://mackerel.io/ja/) からメールアドレスで登録しました。  
サインインするとオーガニゼーション、サービス、ホストを作るスタートアップガイドがあったので、その通り作りました。適当にネーミングしたの、、やっぱ失敗だったかな。  

エージェントのインストールはrpmが選べるみたいだったので、その通り、叩いてみた。  
特にひっかかりなく、agentもインストールできたんですが、sedでapikeyを書き換えるんですね。起動時にuser/passの入力を想像してたんで、ちょっと以外でした。  

で、mackerelのページに戻ったら、もう既にcpuやらmemoryやらの6個ぐらいのグラフが出来上がってた。こいつぁ、早い！って感心しました。  

#### Hubotを監視してみる {#no2}
Slackである形式で投稿したら、メールも送信してくれるみたいなHubotのscriptを作ってたんですが、v3のHubotで運用していたせいか、3,4日ほど利用されないとSocketが切れるのか、Hubot自体もKILLされてるみたいな現象が起きてました。  
休み明けに会社来て、bot無反応だったよって聞くたびに悲しくなったので、モニタリングしてみたかったのでカスタムメトリックなるものを作ってみました。  
```
[Tue Dec 22 2015 01:31:13 GMT+0900 (JST)] ERROR Last pong is too old: 14.844
[Tue Dec 22 2015 01:31:13 GMT+0900 (JST)] INFO Reconnecting in 1000ms
[Tue Dec 22 2015 01:31:14 GMT+0900 (JST)] INFO Attempting reconnect
[Tue Dec 22 2015 01:31:14 GMT+0900 (JST)] INFO Connecting...
[Tue Dec 22 2015 01:31:43 GMT+0900 (JST)] INFO Slack client closed, waiting for reconnect
[Tue Dec 22 2015 01:32:18 GMT+0900 (JST)] ERROR undefined
[Tue Dec 22 2015 01:32:18 GMT+0900 (JST)] INFO Reconnecting in 2000ms
[Tue Dec 22 2015 01:32:20 GMT+0900 (JST)] INFO Attempting reconnect
[Tue Dec 22 2015 01:32:20 GMT+0900 (JST)] INFO Connecting...
[Tue Dec 22 2015 01:32:21 GMT+0900 (JST)] INFO Logged in as <むふふ>, but not yet connected
[Tue Dec 22 2015 01:32:22 GMT+0900 (JST)] INFO Slack client now connected
error: Forever detected script was killed by signal: SIGKILL
```
落ちてた時のログがこれ。  
どうもKILLシグナルが出てるみたいだから、プロセス監視できたら良いかなーって思ってたら、こちらの方と全く同じ要件でした。。確かに私もよくわからなかったです。(´・ω・｀)  

{{< cite url="https://arata.hatenadiary.com/entry/2015/06/23/005339" >}}

で、いろいろ調べてると、公式プラグイン集なるものがGitHubに集められていて、またまたrpmでインストールできる事が分かりました。で早速入れてみました。  

[mackerelio/mackerel-agent-plugins](https://github.com/mackerelio/mackerel-agent-plugins)  

* mackerel-plugin-apache2
* mackerel-plugin-php-apc
* mackerel-plugin-memcached

また特になんのひっかかりもなくインストールできて、`/usr/local/bin`に監視スクリプトが配置されていました。[ヘルプ](https://mackerel.io/ja/docs/entry/howto/mackerel-agent-plugins)にある通り、`/etc/mackerel-agent/mackerel-agent.conf`のコメントアウトを解除してあげたら即反映されてました。  
Apacheだったらserver-statusを見れるようにしとくとか、段取りがあるプラグインもありますが、とってもお手軽でした。Sensuは触ったことないけど、munin触ったことあったらつまづく箇所ないんじゃないかっていうぐらいでした。

ということで、どうやら`/usr/local/bin`にmackerel-pluginから始まるスクリプトを用意して食わせるって感じだったので、シェルスクリプトだけど作ってみました。  

_at /usr/local/bin/mackerel-plugin-hubot_
```
#!/bin/sh
echo -e "<むふふ>-hubot\t`ps aux|grep hubot|grep adapter|wc -l`\t`date -u +%s`"
```
_at /etc/mackerel-agent/mackerel-agent.conf_
```
[plugin.metrics.<むふふ>-hubot]
command = "/usr/local/bin/mackerel-plugin-hubot"
```

文字列 Tab 数字 Tab `date -u +%s`な感じで標準出力したら、うまいこと読んでくれました。こんなんでも食ってくれるんだったら、perlでもphpでもなんでも作れそうです。公式プラグイン集はGo言語が多かったようです。agentがGo言語製だからかな？

#### 監視ルールを設定してみる {#no3}
Monitorsの監視ルールを追加から、作ったメトリックのルールを追加してみました。監視対象のメトリックから、絞り込んで設定。プロセスが1未満だったらWarningにもCriticalにもする的な設定にしてみました。  
どうでもええけど、不等号がどっちやったっけ？ってちょっとだけ迷いました。

#### Slackに投稿  {#no4}
Monitorsのチャンネル設定から設定しました。アラートはEmailもSlackもチャンネルって呼ぶそうです。  
SlackはIncoming WebhooksのURLを設定すればいいみたいですね。  
で、おもむろにHubotをstopしてみたら、グラフ付きでちゃんと通知来ました！メールボムみたいにギャンギャン通知くるのかなー？どうなのかなー？って10分ほどおいてみましたけど、そうでもなかったです。これは嬉しいですね。  
待ってるの飽きてきたので、startしたら、グラフ付きでちゃんとokきました。

#### SlackからHubotを再起動  {#no5}
Slash CommandsからJenkinsAPIを叩いて行けそうだったんですが、認証通すのがしんどかったので、それ用のWebAPIを用意した方が速そうって気付きました。  
でも、もはやMackerel関係ないし、時間切れだしで、また今度作ろうと心に誓って作業終了にしました。

#### まとめ  {#no6}
一通りmackerelを触ってみた感想は、既存のツールを意識した作りにしてるのかなーって思いました。今使ってるツールのこうならんかなー的な欲求を汲んでくれてるかなと。  
Zabbixのグラフ探すのが未だに苦手、muninの通知設定苦手〜とか。。あれ、俺だけか・・・  

ブログは、、あれですね。書くのに超時間かかりました。。世のブロガーさんはすげーなって感心です。もっと画像とか入れたら華があったんだろうなぁ〜。
