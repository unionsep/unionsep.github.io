---
title: "Zephirはじめてみました"
date: 2017-06-30T19:04:32+09:00
draft: false
toc: false
images:
tags: 
  - untagged
---

どうも

{{< cite url="http://unionsep.hatenablog.com/entry/2017/04/13/162309" >}}

前回の記事で少し話しましたが、Zephirを触ってみました  
ZephirはPHPカンファレンス2015ぐらいに見聞きしていて、興味のあるプロダクトでした


{{< cite url="https://zephir-lang.com/" >}}

{{< cite url="http://phpcon.php.gr.jp/2015/" >}}

ただ、インストールしてみるものの、なかなか実際にどんなものか確かめるまでに至らなかったのですが、おもむろに見てみたらバージョンが `0.9.8` になっててそろそろ正式版になるのではと思って焦って触りましたｗ

---

[Installation](https://docs.zephir-lang.com/en/latest/install.html) に `If you’re using Ubuntu` って書いてあったので、手持ちのUbuntu16.04にインストールしてみました  
ホント、最近 `apt` でなんでもインストールできますよね  
みんなUbuntu好きなのかな

インストール自体は書いてある通りに `gcc` やら `PHP` を入れて `git clone` して installer叩くだけなので、手順は割愛します

で、とりあえず [Tutorial](https://docs.zephir-lang.com/en/latest/tutorial.html) にあるものを作ってみました  
他でも書かれてありますが、ちょっとコードを書いてPHPとの違いを意識したのは以下ぐらいでした

- 変数の書き換えに `let` を指定
- `namespace` は必須
- `for` は `foreach` になる
- `if` で条件式に括弧いらない
- 型がPHPより厳密
- functionの戻り値の型をhintで指定できる

まぁ、JavaとかC#とか触ってる人だったら、あんまり意識しなくても自然な流れで書けるのではないでしょうか  
聞いていた `elseif` が使えないというのも、このバージョンでは使えるようになっているみたい

{{< title url="https://docs.zephir-lang.com/latest/introduction/#if-statement" >}}

---

でですね、再帰的なarray_searchを作ろうと思ってこんなコードを書いてみたわけですが、結構ハマりました  
書いたと言いましたが、多分これじゃない感じなんですけど、それっぽいものが書けたので、とりあえずコードは載せようと思いまして^^;  
どうもZephirにはPHPの `use` に相当する機能（参照渡し）がないようで、どう解消しようかハマっていました  
結果どうなのか追えていないのですが、以下あたりで議論されてるみたいです

{{< cite url="https://github.com/phalcon/zephir/issues/1318" >}}


```php
namespace Utils;

class Arrays
{
    private static keys;

    public static function arraySearchRecursive(var needle, var heystack) -> array
    {
        let self::keys = [];
        self::search(needle, heystack);
        return self::keys;
    }

    private static function search(var needle, var heystack) -> bool
    {
        var key, val;
        for key, val in heystack {
            if is_array(val) {
                self::search(needle, val);
            } else {
                if needle == val {
                    let self::keys[] = key;
                }                
            }
        }
        return;
    }

}
```

---

どうやらSuperGlobal、_POSTの値とかも取れるみたいで、取る必要ないんちゃうかなとか思いました^^;  
言語仕様は、そこまで込み入った感じはなかったのですが、`function` に `->` 指定で返却値の型をhintで書けるぐらいが独特かなぁというぐらいでした

{{< title url="http://tech.cygames.co.jp/archives/2976/" >}}

こちらの記事にも書かれている通り、配列処理で高速化が見込めるそうです  
僕も計測をしてみたかったのですが、わりとハマったので一旦ここで断念しましたが、ソーシャルゲームを開発していた時は、巨大な配列処理を行う事が結構あったので、ガチャロジックとか開催中のイベント配列とかで使えないかなと思いました  
あと、単純にextension化することで、簡単に処理を変更できなくするという意味でも有効性を見いだせる気がしています

今はゲーム事業から離れてしまったので、Zephirを使って高速化を試みるより、リファクタリングやDBの正規化で事足りますが、いつかキラーコンテンツとして提案できる日が来たらいいなぁと思っています  
いつか使うど！

（UbuntuデフォのFirefoxではてな開くと英語になったのにちょっと感動した！）