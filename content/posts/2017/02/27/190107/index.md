---
title: "jQuery.DataTablesで完全一致検索"
date: 2017-02-27T19:01:07+09:00
draft: false
toc: false
images:
tags: 
  - jquery
---

はまった〜

知らなかったけど、とっても便利なjQueryのプラグインがあって、むしろ便利過ぎて使い所迷っちゃうなって感じ  
ページング実装するだけで使ってもいいじゃない  
2年前ぐらいの記事が多かったから、わりと前から流行ってたみたいなんで、使い方については先人様に任せます  

{{< title url="https://datatables.net/" >}}

で、これ、インクリメンタルサーチっぽい動きがデフォルトで、とってもかっちょいいんですが、使い所によっては完全一致させたいとかあるのかも  
キーワードを入れてサブミットさせて、テーブルに並べるデータをHTML生成時に絞ってやるって方法が真っ先に頭に浮かんだんす  
実装で迷ったのは、JSでスイスイ検索できるのに、完全一致させるためだけにサーバサイドに頼るってのもかっちょ悪いなと  
なんか知らないけど、 `DataTable()` で指定するパラメタにちょちょいって書いてやれば、すぐできんじゃない？って思ってました  

そんなの速攻できるっしょってたかをくくってたんですが、わりとそうでもなくて1日かかっちゃいました  
まぁ、ドキュメントは英語だし、このプラグインで完全一致させたいなんて思うやつはあんまりいなかったのかなぁ  
で、 `jquery.dataTables.js` の中身を読む旅に出発  

```js
$('#example').DataTable(
  "oSearch": {"bSmart": false}
)
```

{{< title url="https://datatables.net/docs/DataTables/1.9.0/DataTable.defaults.oSearch.html#bSmart" >}}

まずこれでスマートなフィルタリングを使わないに設定  

```js
$('#example')
  .DataTable()
  .search("^" + $('#text_area').val() + "$", true, false, true)
  .draw();
```

つぎにこれで正規表現を使って1語検索をするのが正解みたい  
結局stack overflowを参考にさせていただきました  

{{< cite url="http://stackoverflow.com/questions/8609577/jquery-datatables-filter-column-by-exact-match" >}}

ここにも書いてましたけど、stack overflowのが僕にとってやさしかったです

{{< title url="https://code.google.com/archive/p/jquery-datatables-column-filter/issues/137" >}}

いいじゃんいいじゃんって嬉しがってたら、また一難  
テキストエリアにキーワードを入れて、次に消していって、最後まで消したら、最後の検索が残ってる(´・ω・`)  

```js
if ('' != $('#text_area').val()) {
  $('#example')
    .DataTable()
    .search("^" + $('#text_area').val() + "$", true, false, true)
    .draw();
} else {
  $('#example')
    .DataTable()
    .search('')
    .draw();
}
```

テキスト内にキーワードが入ってないときは空で `draw()` したら、とりあえずは期待通りの動きをしてくれた〜  
`clear()` とか `destroy()` 使ったら、デフォルトで設定したパラメタも初期化されてしまってどうしていいかわからなかったｗ  
`oSearch` とか `aoColumnDefs` とかで設定できるのかなと思ったんだけど、ここまでにたどり着くまで半日かかってるので燃料が切れて一旦終了。。  

なんでこの記事を書こうと思ったかというと、DataTablesは調べれば調べるほど高機能なプラグインだなーって思ったんですけど、便利過ぎて完全一致なんてパラメタですぐっしょって思ってしまったから  
だから、多分、次同じことをしようとした時、まーた性懲りもなくすぐっしょて思いそうだったから。。  
これだけスイスイ検索できることがウリ？なのに、完全一致検索は違うんじゃない？って今になって思います(´・ω・`)    

ブログを書く訓練として、思いついたことを書くのを最近心がけてますが、結構ネタはあるみたい  
ただ、やっぱまだ書くのに時間がかかってて、もっと精進せねばと思う感じです〜
