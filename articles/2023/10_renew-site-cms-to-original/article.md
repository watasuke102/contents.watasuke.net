---
title:        'サイトのバックエンドを自作CMSに乗り換えました'
tags:         ['programming', 'react', 'rust']
is_favorite:  false
published_at: '2023-12-17T16:55:50'
updated_at:   '2023-12-17T22:00:17'
---

これが新 CMS によって構築され直された watasuke.net か～～！！！（予定）

というわけで、ここ最近取り組んできた自作 CMS がいい感じになってきたので、こうして記事を書いています。少なくともこの記事が書ける程度の完成度にはなっています。この記事が公開される頃には、watasuke.net の CMS は完全に置き換えられていることでしょう。

## 背景

これまで、watasuke.net では CMS としてヘッドレス CMS の一種である Strapi を用いていました。何かしらで使っている人を目撃したことがあり、ヘッドレス CMS を導入しようと思ったとき真っ先に候補として挙がってきたのがこれでした。

そんなわけで、サイトの公開日である 2021-05-02[^release-date]以来ずっと Strapi を使ってきました。サイト公開のツイートはこんな感じだったらしいです。なつかしいなあ。まだドメインが.tk だった頃の時代です。

https://twitter.com/Watasuke102/status/1388652910049861633

[^release-date]: [最初の記事](https://watasuke.net/blog/article/remake-my-homepage/)を公開した日付は 05/01 らしいが、ツイートしたのは 05/02

はい、ずっと使っていました。**アップデートを行わずに。** 気が付くと、執筆時点での Strapi のバージョンは 4.15.5 となっていました。ではこのサイトで使っていた Strapi のバージョンは？ **3.6.9** です。エグすぎる……

もちろん移行しようとは思いましたし、ToDo にも入れて、実際に手元の Docker 環境で試してみたりしましたが、あまりに面倒だったのでやめました。

![Strapi v4に移行する、という旨のToDo](/img/todo-update-strapi.jpg)

また、Strapi から移行しようと思った別の理由として、ブログ記事などをファイルとして持っておきたかったから、というものがあります。Strapi はデータベースに情報を保存していて、なんとなく（docker-compose を書いて）MySQL を使用していたのですが、必要なときにすぐ Markdown が取り出せたら嬉しいのになあ、と思っていました。

加えて、docker-compose で MySQL を使う際、当然ながらデータを永続化する必要があり、そのための volume をてきとうに設定していたら、パーミッションの関係で Scaffdog を導入できなくなってしまいました。MySQL を root で動かしていて、volume も root になるので、ファイル一覧の取得で Permission Denied みたいなエラーが出てきて悲しかったです。

## なぜ自作？

ロマンです。

正直自作する必要はそこまで無くて、何なら Gatsby のプラグインをそのまま使っても問題はないはずでしたが、いつか Gatsby から乗り換える事になった時が大変そうだなあと思ってしまいました。

そもそも、今の時点で Gatsby への依存はかなり強いです。これ以上依存の道に踏み入るのはまずいと思ったのが理由としてあります。

## 技術スタックなど

GitHub で公開しています。star お願いします！！

https://github.com/watasuke102/cms.watasuke.net

watasuke.net の CMS はバックエンドとフロントエンドの両方を持っています。バックエンドはコンテンツの管理やGraphQLを、フロントエンドはブログ記事の編集を行います。

### バックエンド

Rust を使いました。こういう領域で Rust が最適かと言われると即座に肯定できませんが、折角なので一番好きな言語にしました。後述しますが、まあまあ大変でした。Rust のせいなのかどうかはわかりません。

GraphQL サーバーを構築するため、[Juniper](https://github.com/graphql-rust/juniper)と[Rocket](https://rocket.rs/)を用いています。以前の開発で行ったような、スキーマファーストなバックエンド開発は叶いませんでしたが、Juniper は `schema.graphql` を吐き出すことが出来るので、フロントエンドではスキーマファーストでけっこういい感じな開発になったと思います。

Juniper を動かせる Web フレームワークはいくつかあるのですが、その中でも Rocket にした理由は、Juniper が提供している example の中で最も記述量が少なかったからです。実際かなり楽に開発できた印象があります。特に新規画像の登録まわりは実装前に予想していた以上に簡単に実装することができ、かなりのうれしさがありました。

あと、Rocket（と Juniper）のおかげで、エラーメッセージをちゃんとすることのモチベーションが発生したので、anyhow を初めて使いました。噂には聞いていたもののピンときていなかったのですが、いざ使うとその強力さに驚きました。一番最初は Result の型いろいろあってダルいからという理由で導入して、それだけでも十分ありがたかったのに、`with_context()` や`bail!`, `ensure!` のような強力な機能の数々に助けられました。まあこんなこと言ってますが現状のエラーハンドリングはかなり雑です。もうちょっと問題を一瞬で特定できるよう、今以上に `with_context()` を使う箇所を増やしたいです。

### フロントエンド

Next.js を使いました。app router を使いたかったからです。初めの方は色々あって app router を断念しかけていたのですが、なんとかなりました。ベストプラクティスはまだ全くわかっていませんが、けっこういい感じに開発できてよかったです。

ヘッドレスな UI コンポーネントライブラリとして[Radix UI](https://www.radix-ui.com/)を導入しました。経験上、UI コンポーネントを 1 から作るのはダルいです。最初は Adobe 製の[React Aria](https://react-spectrum.adobe.com/react-aria/)を使おうかと考えていたのですが、最終的に Radix UI を使う決め手となったのは Toast コンポーネントの存在でした。基本的にほしいコンポーネントはだいたいあるし、動きも不満はなく、アニメーション含むスタイリングに必要な CSS 変数などはすべて提供されているので嬉しいです。しかし、CSS のセレクタを多用する[^css-selector]ので、vanilla-extract との相性は若干悪いかもしれません。globalStyle で対処自体はできますが、ちょっと大変です。

[^css-selector]: `.toast[data-state='open']` など

GraphQL とのやりとりを行うコードを、事前に記述していた GraphQL のスキーマ・ドキュメントファイルから生成する graphql-codegen を採用しています。今年の高専祭用に作成した[ImGrate](https://scrapbox.io/watasuke/ImGrate)で採用して、開発体験が良かったのでまた使うことにしました。

これは宣伝なのですが、高専祭についてのブログも書いているので是非読んでみてください。もう一つの高専祭 Web サイトについても書いてます。

https://watasuke.net/blog/article/nituc-fes-2023-pr-works

### 動作のしくみ

まずバックエンド側の `config.toml` を作成して、コンテンツの場所を指定します。コンテンツが格納されるディレクトリを `contents` とすると、それは以下のような構造になっています：

```
contents/
├── articles/
│  ├── 2022/
│  │  └── 00_test-2022/
│  │     ├── article.md
│  │     └── image.jpg
│  └── 2023/
│     ├── 00_test-2023/
│     │  └── article.md
│     └── _not-published/
│        └── article.md
├── sitedata/
│  ├── profile.md
│  └── short-profile.md
└── tags.toml
```

`contents/article` 下に、まず記事を作成した年でディレクトリを分けて、次に各記事のディレクトリを作成しています。ディレクトリ名はそのまま slug として利用しています。記事は `article.md` にマークダウンとして記述されていて、frontmatter にタイトルや公開日などの情報を記録しています。

まだ公開していない記事のディレクトリは `_` から始まります。公開済みであれば、その公開した順に 2 桁で index を付与しています。

このような構造にしているのは、CMS に頼らずともいい感じに見えるようになってほしかったからです。index を付与しているのはファイラでもソートしやすく、公開した順番がわかりやすいからです。そして、`article.md` という名前も、当初は `index.md` とするつもりだったものの、a から始まったほうが上に表示されるのでは？と思ってこの名前になりました。

ちなみに、見ての通り、画像も各記事のディレクトリに格納されています。どの画像がどの記事で利用されているのか一目瞭然ですね！副作用として、他の記事で利用した画像を再利用できなくなってしまいましたが、今のところそのような画像はなかったのでしばらく大丈夫そうです。

graphql-codegen によって生成された関数を呼び出すと、フロントエンドは GraphQL を用いてバックエンド側にリクエストを送信します。バックエンドは**リクエストが送信されるたびにディレクトリを読み取って**、結果を返します。最初は Rocket が提供している Context に格納された配列の参照を返していただけだったのですが、記事が更新される等で Context を可変にしようとすると様々なつらさが出てきたので、このような感じになりました。こんなことして良いのかなあと不安ではありますが、まあ今のところ十分に速くて大丈夫そうなので、だめそうになってきたらまた考えます。

## 以前と比較しての利点

古い Strapi をずっと使っていた故の不満だったかもしれませんが、どうあれ諸々が改善されました。

### 本番環境とほぼ同じプレビューが出来るようになった

実際のブログとほぼ変わらないコンポーネントを使ってプレビューを表示しているので、いざ公開してみたら思ってたのと違った……みたいなことが発生しないと思います。

Gatsby で動かす前提のコンポーネントだったので、Next.js から表示できるようにするためにリファクタリングをしたり小細工をしたりする必要があって大変でした……。

### クリップボードからの貼り付けで画像を追加できるようになった

Strapi を使っていて最も不満だったものはこれです。画像の追加がだいぶ面倒だったのですが、ようやく解消されました。Win+Shift+S とかで画像をコピーし、画像追加ダイアログを開いて Ctrl+V で画像を追加できます。さっき出てきた ToDo のスクショはまさにこの方法でアップロードしたものです。

textarea に貼り付け出来るようにするべきかもと思いましたが、アップロードする画像のファイル名は変更できるようにすべきだと思ったので、通常通りファイルとして存在する画像のアップロード画面とインターフェースを統合しました。

ちなみに画像ファイルの D&D でも追加できるようにしています。これは[react-dropzone](https://github.com/react-dropzone/react-dropzone/)のおかげで、D&D もファイルダイアログからの選択も、そしてクリップボードからの貼付けも、すべて ArrayBuffer でいじることができて良かったです。が、いつか自前実装に乗り換えたいと思っています。そもそもこの全部 ArrayBuffer という嬉しさがライブラリのおかげかどうかもわかっていません。

### 記事編集ページから新規タグを作成できるようになった

Strapi を使っていて少し不満だったのはこれです。新しいタグを作りたいと思ったとき、まずタグの管理ページに飛ばないといけなかったので面倒でした。今では既存のタグを作成する画面から即座にタグを生やすことができます。1 度に 1 つしかタグを作成できませんが、まあ良いでしょう。必要になったら自分で変えれば良いので。

というか地味にこの「不満なら自分で変えれば良い」という状況になったのは嬉しいですね。

## 問題・今後の展望

とりあえず年末に間に合えば良いという気持ちで作っていたのでめちゃくちゃ雑です。来年ガッツリ手を加えようと思います。まあそれでも API 周りに変更はないと思いますが……。GraphQL なので（？）、ある程度デカい変更入れても大丈夫なんじゃないのと思いながら開発しています。

今のところブログ記事に関する情報と、プロフィール[^why-profile]くらいしか管理していませんが、今後はポートフォリオ用の情報も格納していきたいです。それから、RSS を生成できるようにしたいと思っています。

[^why-profile]: プロフィールは Strapi で管理していたため。ポートフォリオに関する情報は toml ファイルとして watasuke.net のリポジトリに置かれています（おそらくポートフォリオに toml ファイルを採用するときは CMS の乗り換えを検討していたから）

あと主にバックエンド側のテストを書きたいです。とりあえず年末に間に合わせるためにテストを作りやすい構造を意識するだけにしよう！と思っていたのですが、それすら途中で諦めました。今後のリファクタリングに期待します。

あと、リアルタイムプレビューなのでかなり重いです。といってもある程度であれば問題ないのですが、長文になればなるほど重くなります。Markdown を変換しているので、段落が増えることはすなわちタグが増えることを意味します。そりゃ重いよな～

## おわりに

とりあえず年末までに間に合ってよかったです。2023 年の振り返りはこの CMS で書きます。

来年になったら、まずは軽量化を目指して頑張りたいです。どうすればいいんだろう……。