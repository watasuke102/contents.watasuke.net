---
title: "Serenity (Rust) でDiscord Botを開発するときに躓いたところ"
tags: ["programming", "rust"]
is_favorite: false
published_at: "2021-12-05T03:59:19"
updated_at: "2022-11-27T06:22:48"
---

自分は同じクラスの人間が集まる Discord サーバーに入っているのですが、わりと VC が活発なんですよね。ただ、「人がいたら入ろうかな」という人も一定数いて（自分もそうです）、そういう人々はいちいち Discord をチェックしなければいけません。面倒ですよね。

ということで、Discord の VC チャンネルに誰かが入退出したら通知してくれる Bot を作成しました。たぶん探したらたくさんあるとは思いますが、今回は Rust と Bot 作成用クレートの Serenity を学ぼうと思い、自分で書きました。

今回は、制作過程で難しかったところ、日本語でまとめておけばわかりやすそうだなと思ったことをまとめておこうと思います。

---

この記事は[共同開発鯖 Advent Calendar 2021](https://qiita.com/advent-calendar/2021/growthers)の 5 日目の記事です。昨日は[鯖民向け Blog の使い方](https://blog.growthers.dev/Growthers/posts/how-to-post/)でした。鯖民向けのブログがあるのにこっちで書いてます。あと、今回の Bot はこの鯖にいる Bot を丸パクリしたものです。ゆるしてください。

## どんなやつ作ったの

これです。

https://github.com/watasuke102/discord-voicechat-notice

誰かがどこかのボイスチャンネルに入ったり出たりすると、あらかじめ指定したチャンネルに、こんな感じのメッセージを送ります。

![discord-voicechat-noticebot.png](/img/discord_voicechat_noticebot.png)

## わからないポイント 0：そもそもドキュメントが読めない

Rust のクレートはかなりしっかりドキュメントが整備されていることが多いですよね。ビルドツールそのものにドキュメント生成機能が備わっているだけあります。

もちろん Serenity のドキュメントも用意されています。

https://docs.rs/serenity/latest/serenity/

ただ、開発初期はこのドキュメントを**ぜんぜん読めませんでした**。正直どこに何が書いてあるのかわかりません。

これはどうしようもないと思います（？）。example を見ながら、わからない構造体をぜんぶ検索欄に入れましょう。検索欄は結構役に立ちました。

https://github.com/serenity-rs/serenity/tree/current/examples

## わからないポイント 1：VC に入ったかどうかはどうやって検出する？

そもそも VC に入ったタイミングがわからなければおわりです。

ということで、とりあえず examples の一番上、`basic_ping_bot`でも見ましょうか。

```rust
// コメント等、いろいろ省略
struct Handler;

#[async_trait]
impl EventHandler for Handler {
    async fn message(&self, ctx: Context, msg: Message) {

// 中略

#[tokio::main]
async fn main() {
    let token = env::var("DISCORD_TOKEN").expect("Expected a token in the environment");
    let mut client =
        Client::builder(&token).event_handler(Handler).await.expect("Err creating client");
    if let Err(why) = client.start().await {
        println!("Client error: {:?}", why);
    }
}
```

構造体 Handler に、EventHandler を impl しています（表現が正しいのかわからん）。そして、Handler はクライアント生成の時に使われるっぽいです。

ということで、ドキュメントで EventHandler を検索しましょう。`serenity::prelude::EventHandler`がそれっぽいので開き、ページ内検索で`channel`とか`voice`とか調べてみます。

ヒットしたのは`voice_server_update`と`voice_state_update`。それぞれ説明文を見てみます。

`voice_server_update`はこんな感じ。

```
Dispatched when a guild’s voice server was updated (or changed to another one).
Provides the voice server’s data.
```

`voice_state_update`はこんな感じ。

```
Dispatched when a user joins, leaves or moves to a voice channel.
Provides the guild’s id (if available) and the old and the new state of the guild’s voice channels.
```

ということで、見るからに`voice_state_update`っぽいですね。これを使います。

（最初の方、間違えて`voice_server_update`と書いててずっと悩んでいました）

## わからないポイント 2：入退出はどう検出する？

誰かが VC に入退出した時に関数が呼ばれる（だけでなく、ミュートにしたときなども呼ばれるのですが、このときは気づかなかった）というのはわかりましたが、入室と退出はどうやって判定するのでしょうか？

これはもう引数をぜんぶ println!で出力して比較しました。old は`Option<VoiceState>`で、new が`VoiceState`なのですが、old が Some かつ`new.channel_id`が None であれば Leave、old が None であれば Join ということがわかりました。

これも VoiceState をドキュメントで検索して、それを見つつやりました。

## わからないポイント 3：メッセージ送信

入退出が検知できたら 9 割完成です。あとは適切なタイミングでメッセージを送れば・・・メッセージを・・・送る・・・？

メッセージの送り方がわかりません。example を見ましょう。またもや`basic_ping_bot`です。

https://github.com/serenity-rs/serenity/tree/current/examples/e01_basic_ping_bot

```rust
    async fn message(&self, ctx: Context, msg: Message) {
        if msg.content == "!ping" {
            if let Err(why) = msg.channel_id.say(&ctx.http, "Pong!").await {
                println!("Error sending message: {:?}", why);
            }
        }
    }
```

Message 構造体がいるらしい？どうしよう・・・

散々悩んで、Twitter の人に助けてもらいました。というかよく読めばわかるのですが、**ChannelId に say 関数がある**のでこれを使えば良いです。

## わからないポイント 4：設定からチャンネル ID を読み込む

といっても設定自体の読み込みは serde_json にポイすれば終了で、問題は「構造体（Handler）からどうやって設定を読み込めば良いのか？」です。

examples フォルダを流し読みすると`global_data`という、いかにもなサンプルがあるので見ます。

https://github.com/serenity-rs/serenity/tree/current/examples/e12_global_data

```rust
struct CommandCounter;

impl TypeMapKey for CommandCounter {
    type Value = Arc<RwLock<HashMap<String, u64>>>;
}

struct MessageCount;

impl TypeMapKey for MessageCount {
    // While you will be using RwLock or Mutex most of the time you want to modify data,
    // sometimes it's not required; like for example, with static data, or if you are using other
    // kinds of atomic operators.
    //
    // Arc should stay, to allow for the data lock to be closed early.
    type Value = Arc<AtomicUsize>;
}
```

見てもわかりませんでした。`Arc<RwLock<HashMap<String, u64>>>`って何だよ・・・。

ただ、これは書き込みもできるデータ構造で、下に書いてあるとおり、読み込み専用なら`Arc<hoge>`でよさそうです。hoge 内には登録したい型が入るらしく、String なら`Arc<String>`です。今回は Setting 構造体を使いたいので`Arc<Setting>`となります。

そして、クライアントを生成した後、こんな感じで登録します。

```rust
let file = File::open("env.json").unwrap();
let settings: Settings = serde_json::from_reader(BufReader::new(file)).unwrap();
{
    let mut data = client.data.write().await;
    data.insert::<Settings>(Arc::new(settings));
}
```

EventHandler の関数にはたいてい Context が渡されるので、以下のような感じで読み出せば OK です。最終的に Option 型が返されることに注意してください。

```rust
let data = ctx.data.read().await;
let settings = data.get::<Settings>();
```

さっきのと合わせて、設定ファイルで記述したチャンネルにメッセージを送信するには、こんな感じにすれば良いわけです。

```rust
let ch = ChannelId(settings.channel_id);
ch.say(&ctx.http, "Hello);
```

ちなみに、ドキュメントの`serenity::model::id::ChannelId`を見る限り、say の第 2 引数には Display が実装されてたら（`println!("{}")`で表示できたら？）なんでもよさそうです。

## わからないポイント 5：embed 使いたい

Bot といえばやっぱり embed じゃないですか？テキストだけだとやっぱり味気ないですよね。

また examples を漁ります。`create_message_builder`が良さげです。

https://github.com/serenity-rs/serenity/tree/current/examples/e09_create_message_builder

channel_id.send_message の第 2 引数にクロージャを渡して、その中で embed すれば解決するらしいです。具体的にどんな関数があるのかはドキュメントでわかります。

ちなみに、クロージャ内で全然補完が効きませんでした。変数名にカーソル合わせたら型推論してくれるのになんで補完はしてくれないんですかね・・・。いちおう型注釈すれば治ります。

```rust
ch.send_message(&ctx.http, |m: &mut CreateMessage| {
    m.embed(|e: &mut CreateEmbed| {
        ...
```

## おしまい

情報量が少なくて大変でした・・・公式ドキュメントと examples のおかげでなんとかなったと思います。

今回の制作過程では嫌というほど Option を使ったので、Option 完全に理解しました。今後も Rust を使っていろいろ書いていきたいです。

明日は@kat0h 氏による「Vim で markdown をプレビューするプラグインを作った（最新版）」です（たぶん）。よろしくおねがいします。
