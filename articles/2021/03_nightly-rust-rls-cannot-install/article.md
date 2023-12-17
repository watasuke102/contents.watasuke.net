---
title: "NightlyなRustでrlsのインストールに失敗した時にやったこと"
tags: ["rust", "programming"]
is_favorite: false
published_at: "2021-07-17T16:39:14"
updated_at: "2022-11-27T06:21:28"
---

Rust を勉強しています。やっぱり言語レベルで便利な機能がある言語は素晴らしいですね。C++だといちいち書かなければならないことを、短いコードで書けるのは良いことです。

さて、Rust には stable/beta/nightly の 3 つがあり、中でも nightly は最新のバージョンにアクセスできるよう、**毎日**ビルドが行われているとのことです。すごいですね。

さて、突然ですがこちらをご覧ください。2021/07/17 に[ここ](https://rust-lang.github.io/rustup-components-history/index.html) にアクセスしたときの表示です。

![rlsがビルド失敗続きな件](/img/rust.png)

rls というのは Rust Language Server のことで、VSCode の拡張機能などに用いられるプログラムらしいです。[ここ](https://doc.rust-jp.rs/atcoder-rust-resources/installation/rls.html)に詳しく書いてありそうです。

こんな具合に、rls のビルドが失敗続きであるため、nightly で rls が全くインストールできず、VSCode で一切補完の効かないコーディングをさせられる羽目になりました。

今回は治るまでに何をしたのかメモしておきます。

## やったこと

最初に見つけたのはこの記事です。

https://qiita.com/s4i/items/0538f7fe4874980ccf27

このツールを使おうと思ったのですが、どうやら直近 7 日しか検索されないらしく、1 週間以上失敗が続いている現状では使用できませんでした。

[ソースコード](https://github.com/s4i/cargo-rls-install)を読んでいると、どうやらツールチェインを`nightly-{日付}`に変更すればいいらしい？とわかりました（この辺よくわかっていない）。

先ほどのページから、rls のビルドに成功した最後の日付が 2021/07/06 であることがわかったので、プロジェクトのディレクトリで `rustup override set nightly-2021-07-06` を実行したら治りました。色々してたので、正直このコマンドのおかげ！と断言できないです・・・。

## おわりに

まさかツールチェイン周りで躓くとは思いませんでした。

もう少し知識をつけて、自分が何をやっていたのかちゃんと理解できるようになりたいです。
