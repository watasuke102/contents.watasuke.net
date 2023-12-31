---
title: "Reactの要素にはkeyを入れろ"
tags: ["programming", "react"]
is_favorite: false
published_at: "2021-06-04T14:30:29"
updated_at: "2021-06-04T14:32:31"
---

```
Warning: Each child in a list should have a unique "key" prop.
```

このエラー、Reactを書いたことある人なら一度は目にしたことがあるのではないでしょうか。

Reactでは、それぞれの要素に重複しない「キー」が必要です。わかりやすい説明は[ここ](https://ja.reactjs.org/docs/lists-and-keys.html) にあるのですが、実際どういう事が起きるの？というのはあまり見ない気がします[要出典]。

と思っていたら、まさにキーの問題で想定通りに動かない現象が発生しました。今回は、なぜ不具合が起こったのかを、自戒を込めて簡単に書き留めておきます。

## はじまり

現在、クラス内でテスト対策問題を共有するサービス、 [TAGether](https://github.com/watasuke102/TAGether)を制作しています。GitHubにて公開してるのでぜひスターお願いします（小声）

（しばらくサービスについての話が続きます）

さて、現在このサービスに追加すべき要素の一つに **タグ機能** があります。カテゴリにタグを付与し、検索などの面で役に立てることが出来るものです。  
もちろん今までもタグ機能はあったのですが、その実態は以下のようなものでした。

- 入力はただの文字列
- 半角コンマで区切らないといけない

つまりどういうことかというと、`タグ1,タグ2、タグ3`という入力がされていたら、「タグ1」というタグと「タグ2、タグ3」というタグが付与されます。  
これがなかなか厄介で、カテゴリ間で統一しづらいという欠点があります。例えば1年生の後期期末テストの対策問題を作るのに、社会カテゴリは「1年後期期末」、数学カテゴリは「後期期末テスト」、英語カテゴリは「テスト」といったような具合です。これだと検索するのも不便です。

ということで、タグ専用のデータベースを用意し、ユーザーはタグピッカーみたいなコンポーネントでカテゴリへ追加するタグを選べるようにすれば解決だな、ということで現在実装しております。

うまく説明できてるかわかりませんが、とりあえず下に動画が貼ってあるはずなので見てください。

## 問題発生

ということで、出来たものがこちらになります。

https://twitter.com/Watasuke102/status/1400810244326912001

なんか挙動が変では？

「test」以外のタグをクリックして消そうとすると、testタグが増殖してるように見えます。  
console.logやReact DevToolなどで試してもstateは正常なはず・・・かなり悩まされました。

## 原因究明

原因が判明したのは「もしかしてkeyが重複してるのが悪いのでは」という思いつきがきっかけで、keyを試しにMath.randomに設定してみたところ正常に動きました。

ちゃんとログには「keyが重複してるぞ」ってエラーが流れてたのですが、「デバッグ用の一時的な値だしいっか～」と思って後回しにしたツケが回ってきました。かなしいです。

## おわりに

ということで、keyが重複してしまうとどうなるのかという実例でした。実はkeyを設定しなければいけないというのを知ったのは最近だったのですが、まさかこんな現象が起きるとは思いませんでした。

何か不具合があったときは取りあえずエラーメッセージを潰せばいいんだなぁと思いました。
