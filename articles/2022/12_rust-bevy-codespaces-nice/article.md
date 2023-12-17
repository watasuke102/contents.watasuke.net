---
title: "GitHub Codespaces+Rust+Bevyで最高の開発環境を作ろうと思ったけど駄目だった"
tags: ["programming", "rust"]
is_favorite: false
published_at: "2022-11-19T23:55:04"
updated_at: "2022-11-20T00:00:19"
---

https://twitter.com/github/status/1590978384254959617

GitHub Codespacesが無料で使えるようになったらしいです。15GBストレージ・2コアマシンなら60時間を毎月無料で使えるとのこと。

以前からずっと可搬性の高い環境が欲しいと思っていて、GitHub Codespacesはまさに理想のような環境でした。有料と聞いて少し手が出しづらいなと思っていたのですが、無料になったなら使うしかないな！！！

また、最近Bevyに興味があるのですが、Bevyはビルド結果としてWebAssemblyを吐き出すことが可能です。つまりブラウザでゲームを動かすことが出来るということですね。

おや？Codespacesを使って、どこでもRust+Bebyで開発できれば最高じゃね？？？と思い立ったので、今回は環境構築をしてみます。といっても、タイトルにある通り失敗したんですが。

## リポジトリ

とはいっても、Templateにしているので、普通に使いたいだけだったらREADME見て適当に使ってください。 ~~`Open in a Codespace` して `cargo run` してLive Server立てれば動きます。~~ 動きません

## Rust側の環境構築

とりあえずプロジェクトを作ります。依存関係にBevyを追加しましょう。

```bash
$ cargo new
$ cargo add bevy
```

また、今回はwasm向けにビルドするので、`wasm32-unknown-unknown` ターゲットをインストールしてあげる必要があります。

ついでに、wasmをHTMLから簡単に読み込めるようにする `wasm-bindgen-cli` をインストールします。Codespace外であれば公式が言っている `wasm-server-runner` を使うのが手軽なのですが、Codespace内でこれを使うと何故か `cargo run` したときにコマンドが強制終了してしまうので、このような形にしています。

```bash
$ rustup target install wasm32-unknown-unknown
$ cargo install wasm-bindgen-cli
```

wasm用にビルドするときは、`cargo build --target wasm32-unknown-unknown` のようにすれば良いのですが、面倒なので設定ファイルでターゲットを指定します。ついでに、`cargo run` で `wasm-bindgen-cli` を使うようにしましょう。

**`.cargo/config` （`Cargo.toml` ではない）** に、以下のように追記します。

```toml
[build]
target = "wasm32-unknown-unknown"

[target.wasm32-unknown-unknown]
runner = [
  "wasm-bindgen",
  "--out-name",
  "index",
  "--out-dir",
  "out",
  "--target",
  "web",
]
```

これで `cargo run` すると、`out` フォルダに `index.js` が生成・・・されるはずなんですが。

## 問題

`wasm-bindgen` が動きません。前述した `wasm-server-runner` も同じ現象が起きているんですが、これらを使おうとするとSIGKILLを食らって強制終了します。

Codespace外では何も問題なく動くんですけどね。これらのコマンドが内部でやってる処理が危険だとみなされて終了されているんでしょうか。

Twitter・GitHub・Redditも含めて軽く調べてみたのですが、同じ現象が起きている人は見つかりませんでした。

## うまくできた場合の動かし方

`wasm-bindgen` が正常終了した場合、`out`フォルダに `index.js` が生成されます。

適当な場所にHTMLファイルを置いて、headタグ内に以下のscriptタグを挿入します。必要であれば、`index.js` までのパスをいい感じに置き換えてください。

```html
<script type="module">
  import init from "./out/index.js";
  init();
</script>
```

あとはVSCodeのLive Serverとかを使ってこのHTMLファイルを開けば終わりです。

## おわりに

まあローカルでは動くので100%無駄ではない気もしますが、ローカルでわざわざwasm書き出すくらいなら普通にデスクトップ向けにビルドしたほうが良くねという気もします・・・

## 参考

`wasm-bindgen-cli` について調べると [The wasm-bindgen Guide](https://rustwasm.github.io/wasm-bindgen/reference/cli.html) が出てきますが、書いていない情報がある（例えば `--out-name` の存在とか）ので、`wasm-bindgen-cli --help` のほうで確認するのが良いと思います。

- [GitHub: codespaces-examples/rust](https://github.com/codespaces-examples/rust/)
- [Browser (WebAssembly) - Unofficial Bevy Cheat Book](https://bevy-cheatbook.github.io/platforms/wasm.html)
