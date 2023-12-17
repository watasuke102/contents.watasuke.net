---
title: "GitHubのcompareを、JSの力でもっと快適にする（ゼロからのOS自作入門）"
tags: ["programming"]
is_favorite: false
published_at: "2022-02-24T02:21:00"
updated_at: "2022-07-24T06:19:39"
---

皆さん、OS作ってますか？

「ゼロからのOS自作入門」という本があります。知識ゼロの状態から、C++を用いてOSを作成することができるいい本です。

最近はもっぱらこれをやっていて、基本的には写経による学習をメインとして進めています。ソースコードは[GitHub](https://github.com/uchan-nos/mikanos) に置いてあり、また各章に対応するコミットにタグがついているので、とても学習しやすいです。

ただ、どのファイルが変更されたとか、そういうのを手作業で行うのは大変です。そこで、GitHubにあるcompare機能を利用します。

## GitHubのcompare機能

ブランチやコミットを指定すると、それらの間のdiffを見ることができます。

`https://github.com/{リポジトリ名 watasuke102/watasuke.tkとか}/compare/{古いコミット}...{新しいコミット}`のようなURLにアクセスすることで、これを使うことが出来ます。「コミット」の部分はタグ・ブランチ名でもいけるっぽいです。おそらくどのリポジトリでも使えると思うので、試してみてください。

ちなみに、下に貼っているのは、mikanosのday02aとday03aとの比較です。古いコミットはosbook_day02aというタグ、新しいコミットはosbook_day03aというタグを指定しています

https://github.com/uchan-nos/mikanos/compare/osbook_day02a...osbook_day03a

さて、今示したように、mikanosリポジトリのタグ名は一貫性を持って命名されています。すなわち、`osbook_day{2桁で日付}{小文字アルファベット}`です。

自分は基本的にこのURLを見て、例えばday03aが終わったら、次にday03bを見ようとします。このときにどういう作業をするかと言うと、

1. URLの`osbook_day02a...osbook_day03a`を見る
1. `03a`の部分を`03b`にする
1. `02a`の部分を`03a`を入れる

正直面倒です。ということで、この作業をJSで半自動化しました。

## つかいかた

Chromeで解説しますが、たぶん他でもほとんど同じです。

1. ブックマークバーを右クリックし、「ページを追加」をクリック
2. ブックマーク名を適当に決め、URLに以下の文字列を貼り付け

```
javascript:void(window.location.href=`https://github.com/uchan-nos/mikanos/compare/osbook_day${document.URL.match(/https\:\/\/github.com\/uchan\-nos\/mikanos\/compare\/osbook_day[0-9]+[a-z]\.\.\.osbook_day([0-9]+[a-z])/)[1]}...osbook_day${window.prompt("移動先 (day***)",document.URL.match(/https\:\/\/github.com\/uchan\-nos\/mikanos\/compare\/osbook_day[0-9]+[a-z]\.\.\.osbook_day([0-9]+[a-z])/)[1])}`)
```

お気に入り登録したら、mikanosのcompare画面を開き、先程登録したブックマークをクリックし、入力ウィンドウに見たいタグを入力すればページが自動で切り替わってくれます！

（JavaScriptが実行されます。何をしているのかは後で解説しますが、第三者が作成したJavaScriptは慎重に実行しましょう）

例えば、day02aとday03aの比較画面を見ていて、day03bを見たくなった時、やるべきことは

1. ブックマークをクリック
2. 表示されるウィンドウに「03b」と入力する

これだけです。

## 何をやっているのか

URLから新しい方のタグ名を抽出し、プロンプトに入力されたタグ名と組み合わせてwindow.location.hrefの値をいい感じに書き換えます。

ブックマーク登録用にかなり見づらくなっていますが、つまりこういうことです。

```javascript
(() => {
  let now = document.URL.match(
    /https\:\/\/github.com\/uchan\-nos\/mikanos\/compare\/osbook_day[0-9]+[a-z]\.\.\.osbook_day([0-9]+[a-z])/,
  )[1];
  let to = window.prompt("移動先 (day***)", now);
  window.location.href = `https://github.com/uchan-nos/mikanos/compare/osbook_day${now}...osbook_day${to}`;
})();
```

## おわりに

最近、こういう感じに微妙に便利なものをJSとかCSSとかで作れるようになってきて嬉しい限りです。Webが無くてはならない存在である今、JavaScriptの知識があると情報を自分好みに抽出したり便利なUserScriptとか書いたりできて良いな～～と思いました。
