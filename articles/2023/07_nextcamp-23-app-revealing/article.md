---
title: "セキュリティ・ネクストキャンプ2023：応募課題晒し"
tags: ["seccamp", "lookback-participate"]
is_favorite: false
published_at: "2023-08-15T08:59:52"
updated_at: "2023-08-15T08:59:52"
---

昨年、このような記事を書きました。

https://watasuke.net/blog/article/seccamp-2022-app-publish/

今年もネクストキャンプに選考通過したにもかかわらず、なぜ課題晒しを今まで怠っていたのでしょうか？それには以下の理由があります。

https://twitter.com/Watasuke102/status/1690378043922202624

というわけで、「あなたに対する問い」についてはまた改めて書く気がしています。とりあえず、このページは「セキュリティ・ネクストキャンプ2023に提出した応募課題の内容」ということで、よろしくお願いします。

各見出しに問題文と回答における意図を書いており、また実際に提出した回答は折りたたんでいます。適宜開いたり閉じたりしてください。

## 応募について

体験談でもまた書くと思いますが、僕はギリギリまで全国大会とネクストとでどちらに応募するか迷っていました。いつもどおりのノリであれば全国大会にそのまま応募しただろうし、実際それらの課題を読んで回答を書き始めるところまでは行っていました。ただ、今年は例年と比べてかなり低レイヤに関する、特にCPUとGPUについての講義が多く、応募直前くらいまで悩んでいました。

ただ、全国大会に行った直後にネクストキャンプに行くのはちょっと早すぎでは……？という思いがずっとありました。もちろん期間が短くないと駄目というわけでは全く無いのですが、せっかくネクストキャンプが広い年齢層を集めているのに、全国にも応募可能な段階でネクストに応募するのってちょっともったいなくない？という思考がありました。

ただし。応募規約などをよく読んでいると、どうも参加から5年経てばまた参加権がもらえるらしいんですよね。僕は17歳時点で2022年のキャンプにオンライン参加しました。仮に今年ネクストに参加すると、18+5 = 23歳（多分）で参加権が復活し、これはネクストの応募条件「25歳以下」を満たします。じゃあネクスト行くか〜と思って、ネクスト応募に切り替えました。

ちなみに上の年齢計算が間違ってたらおわりです。まあ1年ズレ程度だと思いますが、03/31時点での年齢だから計算がややこしい……。

応募締切は2023/05/15 23:59 (JST) で、僕は当日07:37に応募しました。応募課題のほうはというと、締切 2023/05/22 23:59 (JST) で、提出時間は<u>残っていませんでした</u>。そんなことある？？メール届かなかったってこと？日記にすら残っていませんでしたが、少なくとも21日の夜に課題を書いている旨の記述があるので、当日提出ということでしょう。

と思ったらTwitterで言ってました。締め切り当日の21:21ですね。

https://twitter.com/Watasuke102/status/1660622014825246720

## あなたに関する問い

```
あなたは今までどのようなことをやってきましたか．どのようなことができて，どのようなことが得意で，どのようなことに自信がありますか．
どのようなものを作りましたか．どのような情報を発信してきましたか．
どのようにしてそうしたことをやってきましたか．なぜ，そのようなことをやってきましたか．やってきてどう思いましたか．
参加できた場合，セキュリティ・ネクストキャンプにどのようなことを期待し，どのようなことをやってみたいですか．
```

自己紹介をします。[プロフィール](https://watasuke.net/portfolio)+αでおわりですね。みなさんもぜひ、プロフィール公開という名の自分語りを軽率に行っていきましょう。

<details>

<summary>回答</summary>

（以下に書いてあるもののうち 8 割程度は [ポートフォリオ](<[https://watasuke.net/portfolio](https://watasuke.net/portfolio)>) や [プロフィール](<[https://watasuke.net/profile/](https://watasuke.net/profile/)>) に書いてあることの要約です。是非これらも見てください）

- 小学 4 年のころ、ゲーム開発に興味を持ち、HSP でプログラミングを始める
  - 「ゲーム開発　やりかた」みたいな感じで調べた時に出てきたから
    - 特にこれでなければならない理由などはなかった
  - RPG やミニゲームを開発していた
- 中学 2 年生で DX ライブラリ & C 言語、3 年生で OpenSiv3D & C++を学ぶ
  - 東方 Project に触れたのもちょうどこの時期で、これに影響を受けた STG を開発していた
  - 中学 3 年生のとき初めて U-22 プログラミングコンテストに応募
    - 学習特化をうたう WYSIWYG ライクなマークアップテキストエディタ、[MarkStudy](<[https://github.com/watasuke102/MarkStudy](https://github.com/watasuke102/MarkStudy)>)
    - これを機に、ゲーム開発ではなくツール開発のほうに傾いていった
- 高専に入学後は、主に身の回りの不便さを解決するものを作っていた

  - クラスで共有している TimeTree の予定を Discord に通知する Bot を開発
    - 1 年生のときに Python の学習も兼ねて開発　[GitHub](<[https://github.com/watasuke102/TimeTree-NoticeBot-rust](https://github.com/watasuke102/TimeTree-NoticeBot-rust)>)
    - 2 年生のときに Discord.py の archive にともなって Rust で再開発　[GitHub](<[https://github.com/watasuke102/TimeTree-NoticeBot-rust](https://github.com/watasuke102/TimeTree-NoticeBot-rust)>)
  - 国産 Linux ディストリビューション、Alter Linux の開発に参加
    - i3wm エディションの開発を主に行い、C++および Qt を用いた [設定変更ツール](<[https://github.com/FascodeNet/alterlinux-i3-manager](https://github.com/FascodeNet/alterlinux-i3-manager)>) の開発も行った
  - 簡易的な所持金管理アプリ、[ExpNote](<[https://github.com/watasuke102/ExpNote](https://github.com/watasuke102/ExpNote)>) を Flutter で開発
  - テスト対策問題を作ってクラス内に共有することが出来るサービス、[TAGether](<[https://github.com/watasuke102/TAGether](https://github.com/watasuke102/TAGether)>) をフロントエンドに TypeScript+Next.js・バックエンドに PHP+MariaDB（後に JavaScript+Express+MySQL に移行）で開発
    - 英単語テスト対策用の CSV ファイルを配布していたものの、修正が大変だったという理由から開発を始めたもの
    - 詳しい過程などは [Scrapbox](<[https://scrapbox.io/watasuke/TAGether](https://scrapbox.io/watasuke/TAGether)>) に
  - ブログやポートフォリオを含む [ホームページ](<[https://watasuke.net/](https://watasuke.net/)>) を Gatsby.js を用いて開発　[GitHub](<[https://github.com/watasuke102/watasuke.net](https://github.com/watasuke102/watasuke.net)>)
  - 2022 年度 未踏アドバンスト事業に採択された、Linux 向け Wayland ベース DE [zwin](<[https://www.zwin.dev/ja](https://www.zwin.dev/ja)>) の開発
    - C・wayland・wlroots・C++・OpenGL・gRPC などなど、様々なライブラリを用いた
  - セキュリティ・キャンプ 全国大会 2022 の CPU 自作ゼミで RISC-V CPU を開発　[GitHub](<[https://github.com/watasuke102/seccamp_2022_riscv_cpu](https://github.com/watasuke102/seccamp_2022_riscv_cpu)>)
  - 台湾で研修留学を行い、OpenCV を用いた画像処理に関する研究を行った　[発表資料](<[https://github.com/watasuke102/measure-vehicle-distance](https://github.com/watasuke102/measure-vehicle-distance)>)

- よく使う（使っていた）言語としては C・C++・TypeScript・Rust・Python・PHP・Dart

  - 静的型付けで、if 等を式として扱うことができて、公式が用意しているものを含めてライブラリや情報が多い言語が好き
  - 今まで使った中で、すべてを備えているのは Rust のみ
    - 低レイヤを触るときはもちろん、そうでないときもコンパイラの厳格さや周辺ツールがコーディングを助けてくれるので非常に体験が良い
  - Python・PHP は型周りで何度も苦しめられた経験があり、これが静的型付けを好む理由の一つとなっている

- 開発してきたものとは方向性がかなり違うものの、低レイヤに関する興味が強いと思っている
  - 身近なコンピューターに関して知らないものを知りたいと思う気持ちが強そう
  - 例えば自作 OS や CPU 自作に興味があり、前者は [プロトタイプ](<[https://github.com/watasuke102/os-proto](https://github.com/watasuke102/os-proto)>) の開発中、後者はセキュリティ・キャンプで RISC-V CPU の開発を行った
  - C 言語をコンパイルし、objdump で ELF を覗いてしくみを調べたりするのも好き
  - 今回のネクストキャンプに応募を決めた理由の 1 つに、昨年より低レイヤに関する講義が多そうに見えたというものがある
    - 特に GPU プログラミングやオリジナル CPU に興味があります（後述）
- 開発のモチベーションは「不便さの解消」で、それ以外（例えば前述の ELF 調査など）は「知らないことを知りたい」というモチベーションが元になっている？

- 開発する過程で何か新しいことを知ったり、新しいものを作れたり（== 進捗が生まれたり）した際、主に Twitter もしくはブログで発信している
  - 記事に書くほど長くなかったら Twitter
    - たまに Twitter で長々とツイートしてしまうこともある
  - ブログの例：[HTML+CSS で任意角度の弧を描画する](<[https://watasuke.net/blog/article/fan-border-with-html-css/](https://watasuke.net/blog/article/fan-border-with-html-css/)>)
    - [ポートフォリオ](<[https://watasuke.net/portfolio](https://watasuke.net/portfolio)>) 開発中に試行錯誤して編み出した、SCSS の mixin をうまく使って弧を描く方法
  - どちらも中学生時代からずっと続けている
    - 中学生時代に書いた記事の例：[DX ライブラリの SetWindowStyleMode について](<[http://menster.wp.xdomain.jp/dxライブラリのsetwindowstylemodeについて/](http://menster.wp.xdomain.jp/dx%e3%83%a9%e3%82%a4%e3%83%96%e3%83%a9%e3%83%aa%e3%81%aesetwindowstylemode%e3%81%ab%e3%81%a4%e3%81%84%e3%81%a6/)>)
- 技術の勉強は、時と場合によってネットと本を使い分けることが多い
  - HSP を勉強していた頃は全てをネットの記事・サンプルコード・公式ドキュメントなどで勉強した
  - C 言語は、「C の絵本」という本を学校の読書の時間に何度も繰り返し読みつつ、DX ライブラリでのゲーム開発を経て学んだ
  - 基本的に「実際に自分でコードを書く」もしくは「サンプルコードを変更して挙動を把握する」がメインな気がする
    - HSP・React は特にこの傾向が強いように思える
- 新しいものが好き

  - Linux はローリングリリースの Arch Linux、Google Chrome は Dev 版、Windows 11 および VSCode は Insider、Discord は Canary……と、新しい
  - GitHub の feature preview も積極的に有効化している

- 今年のネクストキャンプにおける講義は、どれも自分でするのが難しそうなものばかりだから、ここでしか得られない経験を得たい
  - CanSat はその最たる例で、ハードウェアの調達・受講生同士でチームを組む、というのを 1 人でやるのはかなり難しそう
    - 2022 年度の未踏アドバンストでチーム開発をしたときは、大変なことも多かったもののかなり多くのことを学ぶことができたため、ネクストキャンプにおいてもチーム開発の経験を積みたいと思っている
  - GPU プログラミングについても、今「raspberry pi GPU assembly」や「raspberry pi GPU アセンブリ」で探した限りでは、それらしきサイトがかなり少ないように感じられた
    - 情報が少ないと問題解決が大変だから、誰かに質問できる環境で学習できるというのはかなり貴重

</details>

## 課題への姿勢に関する問い

```
自身で何らかの技術的な疑問を設定し，その疑問を解決しようと取り組み，その過程を示すことで，自身の技術力や課題に取り組むやりかたを説明してください．
(疑問の例：実行ファイルはどのような構造になっているのだろう？ lsコマンドは何をしているのだろう？ pingコマンドを実行すると何が起きるんだろう？ といったようなことです)
設定する疑問は何でも構いませんし，解決しなくても構いません．
解決できたかどうかではなく，いかに課題に取り組むかという点を評価します．
```

当時Hyprlandに移行しようと思っていたものの、うちの環境では解像度が正しく設定ファイルどおりに設定されないという不具合がありました。せっかくWaylandのバックグラウンドがあるのだから、この課題のついでにソースコード読んで **_Contribute_** するかあ〜と思っていたものの、時間がなくて出来ませんでした。

以下は課題とは全く関係なしに、ちょっと気になったというそれだけの理由で調べていたものです。同時期に、自作OS関係でLinuxにおけるELFファイルのロード部分も少し読んでメモに残していましたが、文量としても終着点としてもこちらのほうが優れていたため、こちらにしました。

記憶が正しければ、「ふとChromeのアドレスバーに〜」以降、つまり罫線以降の部分は全く改変せずにそのまま提出しています。なんとなくObsidianでメモを残したのですが、正直Notionのほうが良かったと思います。HyprlandのソースリーディングはNotionにメモを作ってやっていました。

<details>

<summary>回答</summary>

少し前、Chrome を使っている時にふと「アドレスバーに URL を入力して Enter を押した時、ブラウザ側で何が起きているのか」という疑問（よく「名前解決のために DNS に問い合わせて～」という回答が返ってくるような、ネットワークに関する疑問ではなく、もう少し文字入力 UI 等に関する処理の疑問です）が湧いたため、Chromium のソースコードを読んでいたので、その過程で用いていたメモをそのまま貼り付けます

最近になって、大規模なコードを読む際には Notion あるいは Obsidian にこのようなメモを残すようにしています（以前はそのまま読んでいましたが、同じ部分を繰り返し読む等が多発したのでメモを取るようにしました）

---

ふと Chrome のアドレスバーに URL を入力した時にどんな処理（主に URL 文字列の処理について）がされているのか気になったため、Chromium を読むことにした

- とりあえず、まず Bard に質問
  - ぼく「Chromium のソースコード内で、Omnibox にテキストが入力された時の処理が書かれている箇所を、ファイルパス及びその周辺のソースコードとともに示してください。なお、GitHub 上にあるミラーリポジトリ（ https://github.com/chromium/chromium ）を参照してください」
    - Chrome のアドレスバー兼検索欄は Omnibox と呼ばれる
  - Bard「Chromium のソースコードで、Omnibox にテキストが入力されたときに処理される場所は次のとおりです。ファイル: `chrome/browser/ui/views/location_bar/location_bar.cc`、関数: `OnInputChanged()`」
- 調べてみましたが、`location_bar.cc`はありませんでした！いかがでしたか？
- とりあえず似ている`location_bar_view.cc`を見る
  - なんか Omnibox についての言及が多かったので、そっちのコードを読んだほうが良さそう
    - なんで Omnibox にたどり着いたのかは忘れた
- `omnibox_view_views.h`に移動
  - 色々見てると `HandleAfterKeyEvent()` が目に止まった
    - `omnibox_view_views.cc:327`で Enter キーが押されたときの挙動を規定してそう
      - Alt キーが押されていたら新しいタブで開くようにしている（知らなかった）
    - `model()->AcceptInput()` が呼ばれている
      - `model()` が返すのは `OmniboxEditModel*`
- `omnibox_edit_model.cc:495` に移動、`OmniboxEditModel::AcceptInput()` を読んでいく
  - `GetInfoForCurrentText()` でなんか `AutocompleteMatch` 型の変数 `match` を設定している
    - おそらく Omnibox に入力した時に出てくる候補とかの選択状況とかを確認し、選択中の候補に対応する URL を取得してそう
      - 引数、参照じゃなくてポインタなんですね
      - `popup_->IsOpen()` かつ `query_in_progress()` っていかにもそれっぽい
      - これが false のときに使われている、`AutocompleteClassifierFactory::GetForProfile(profile_)->Classify()` は何？
        - Autocomplete ～は「Singleton that owns all AutocompleteClassifiers and associates them with Profiles」らしいです（`autocomplete_classifier_factory.h:15`）　知るか
          - Profile は Chrome の機能で、ケバブメニュー（Omnibar がある行の一番右にある︙）の横、自分の Google アカウントのアイコンが表示されているところから管理できるアレ
        - 内部で `AutoCompleteResult` なるものを取得しており、これは「All matches from all providers for a particular query. This also tracks what the default match should be if the user doesn't manually select another match」らしい（`autocomplete_result.h:19`）
        - ここで`view_->GetText()` を呼んでおり、これによって OmniboxViewViews の`textfield_->text()` を取得している
          - `textfield_->SetText(text);` を呼んでいるのは `OmniboxViewViews::SetTextAndSelectedRange()` で、これは `void OmniboxViewViews::SetWindowTextAndCaretPos()` から呼ばれている
            - CaretPos は「Omnibox 内のカーソルの位置を表す」らしい（By Bard）
              - Bard は「構造体」って言ってるけど size_t 型なんだよなあ
          - `SetWindowTextAndCaretPos` 自体は gtk や cocoa 等、実際のフロントエンド実装部分から呼び出されてそう　あと `OmniboxView::SetUserText()`
        - まあどうあれ、ポップアップから自動補完・履歴などの候補を選択していなければ、ユーザーの入力が用いられるという解釈で良いのではないか
  - `match.destination_url` が valid じゃなければ return
    - `destination_url` は `GURL` 型
      - `GoogleURL`？
      - `pyautolib.i` というファイルで定義されている？
        - Python との連携？`pyauto.py` 内にそれらしきものはなかった
    - まあ URL のバリデーションだろう
    - `match` が URL を持っていると考えて良いのかな
  - `view_->OpenMatch()` で多分実際に開く処理を行っている
    - `view_` は `OmniboxView` で、`OpenMatch()` 内部で `model_->OpenMatch()` を呼んでいる
      - で、この `model_` は `OmniboxEditModel` というオチ　`OmniboxEditModel` から `OmniboxView` の `OpenMatch()` を呼んだと思っていたら、いつの間にか `OmniboxEditModel` の `OpenMatch()` になっていた……
- `omnibox_edit_model.cc:534` に移動、`OmniboxEditModel::OpenMatch()` を読んでいく
  - とりあえず引数として渡された `match` は `AcceptInput()` でつくったものと同一だから、これを使っている箇所だけ見ていこう
    - 627 行目あたりがそれっぽい？
      - 拡張機能の URL を指定してたら `ExtensionAppProvider::LaunchAppFromOmnibox()` という関数を呼んでる
        - 本質じゃないから雑にしか読んでないけど、 この関数の最後で `application_launch::OpenApplication()` を呼んでいるらしい
      - URL が拡張機能でない、即ち普通の URL であれば、`match.destination_url` から `GURL` 型のオブジェクト `destination_url` を作成して、`controller_->OnAutocompleteAccept()` に渡す
        - また autocomplete？
        - `controller_` は `OmniboxEditController*` 型
          - そういや今までの型においてポインタ無視してたかもしれない　まあいっか
        - `OnAutocompleteAccept()` の定義元を VSCode に探してもらうと、`LocationBarView::OnAutocompleteAccept()` が出てきた
          - `LocationBarView` は `OmniboxEditController` を継承している
          - `LocationBarViewGtk` も出てきたけど一旦無視
- `location_bar_view.cc:1093` の `OnAutocompleteAccept()` を見る
  - 第 1 引数が `OpenMatch()` の最後で作られた `destination_url` と同じ URL だと思うので、これが使われているところを見る
  - といっても、url が valid なら `location_input_` に代入してるだけ
    - これは `string16` 型　`UTF8ToUTF16(url.spec())` としてから代入している
  - そしてその後、`command_updater_->ExecuteCommand(IDC_OPEN_CURRENT_URL);` という処理が！
    - `command_updater_` は `CommandUpdater*` 型
- `command_updater.cc:44` の `ExecuteCommand()`
  - 引数としてもらった `int id` をそのまま `CommandUpdater::ExecuteCommandWithDisposition()` に渡してその戻り値を return してるだけ
    - `CommandUpdater::ExecuteCommandWithDisposition()` も、`id` に対応するコマンドが使えるかどうかを確認してから、`delegate_->ExecuteCommandWithDisposition()` を呼んでる
      - `delegate_` は `CommandUpdaterDelegate\*
      - `ExecuteCommandWithDisposition()` の定義はいくつかあって、どれも switch 文で `id` に対応するコマンドを呼び出している
        - 最もそれらしきものは `BrowserCommandController` のもので、これ以外（`simple_web_view_dialog` だの `panel` だの）は `IDC_OPEN_CURRENT_URL` に対応するコマンドを用意していない
        - `BrowserCommandController` は、`id` が `IDC_OPEN_CURRENT_URL` のとき `browser_commands.cc@OpenCurrentURL()` を呼んでいる
- `browser_commands.cc:353` の `OpenCurrentURL()`
  - 引数に `Browser*` 型の `browser` を受け取る
  - `browser->window()->GetLocationBar` で `location_bar`を取得し、 `location_bar->GetInputString()` で取得した文字列から `GURL` 型のオブジェクト `url` を作成している
    - `GetInputString()` は `location_bar` の `location_input_` を返しているだけ
    - そう、`LocationBarView::OnAutocompleteAccept()` で代入されていたメンバですね　伏線回収だ～～～
      - これは `OmniboxEditModel::OpenMatch()` で作られた `destination_url` と同一で、こっちは `OmniboxEditModel::AcceptInput()` で作られた `match` をもとにしていた
        - そういえば結局この `match` があんまりよく分かってないような気がする……
  - `url` などをもとに `NavigateParams` 型のオブジェクトを作り、 `Navigate()` に渡す
    - 多分これでオープン処理してそう
    - もう読むのやめて良いような気がするな
- （蛇足？）`browser_navigator.cc:390` の `Navigate()`
  - 523 行目で `LoadURLInContents()` を呼び出していて、この直前に「Perform the actual navigation, tracking whether it came from the renderer」とのコメントがあった
    - 引数として渡しているもので気になるのは `params->target_contents->web_contents()` と `url`
      - 後者については、基本的に `params->url` （つまりアドレスバーの中身だろうな）を代入している
      - ただし、それが空っぽだった場合、プロフィールを参照してホームページの URL に置き換えている
      - 前者について、481 行目あたりに `params->target_contents = chrome::TabContentsFactory(...)` という処理がある
        - 第 2 引数（`content::SiteInstance`）に渡すオブジェクトを作るために `url` を用いている（`tab_util::GetSiteInstanceForNewTab(params->browser->profile(), url)`）
        - この GetSite なんちゃらをたどると `site_instance_impl.cc:241` の `GURL SiteInstanceImpl::GetSiteForURL()` にたどり着くことが出来て、ここで url から origin を取ってきたりしてるらしいですよ

とりあえず URL 入力後の処理はおおよそ掴めたような気がするけど、気になるところも残ってはいる

- `match` の正体と URL バリデーション
  - Python のライブラリっぽさがありそう？
- 実際に fetch してるところがどこなのか
  - `SiteInstance` を辿ったら良いのかなあ
  </details>

### 興味ある分野に関する問い

```
セキュリティ・ネクストキャンプの講義の一覧を見て，その中から興味のある講義を選び，その講義で扱うテーマに対して自分が考えること，興味，疑問，課題，自分なりの考察などを説明してください．
その分野について知識があるかどうかではなく，いかに興味や疑問を持ち，課題を考え，自分なりに調べて考察するかといった点を評価します．
```

講義のテーマに対してのお気持ちを述べました。自分なりの考察や課題についてあまり述べていなかったので、落ちるならここなんじゃないか……と思っていました。

全ての課題に回答しようとは思っていなかったのですが、気づいたら全部に対して何かしらを書いてしまいました。講義全てが興味のある分野で、とても良かったです。

<details>

<summary>回答</summary>

- 学んだ技術の本質から魅力や価値を引き出そう
  - 技術を学んでいても、なかなか本質をつかんだり、得た知識を活かしきれてないように感じることが多い
    - 例えば Zenn にある記事を読んでいる時、その全てを実際に活かせているようには思えない
      - すぐ忘れてしまうから？
    - 「技術の本質が何かを見つめ直す」という考えを持ったことはなかったため、とても気になっている
    - このような思考法は、一度定着させたら長期に渡って利益を得ることが出来るため、ぜひ身につけておきたい
- 低レベル GPU プログラミング
  - 昨年は OpenGL を用いたソフトウェアの開発を行っていた
  - GPU 制御にはかなり興味を持っている
    - CPU やメモリと違い、ハードウェアアーキテクチャの基礎を勉強する時にあまり深く触れられない
      - 少なくとも僕の学校で行われた授業では、CPU はレジスタや ALU まで詳しく説明されるのに対し、GPU は存在に言及されるのみ
    - GPU の制御方法についてあまり言及されないような気がする
      - CPU に関する知識がほとんどなくても、アセンブリを使えば～というような話に触れることがあったものの、GPU に関してそのような経験はない
        - あるとすれば、OpenGL に関する言及くらい？
  - しかし、GPU 制御に挑戦するタイミングをなかなか設けることができなかった上、OpenGL の理解もまだ浅い
  - OpenGL のようなライブラリを介さない制御に関しては全く方法を知らないため、かなり興味がある
  - Raspberry Pi に関しても興味があるもののあまり深く触ったことがないため、この機会に触ってみたい
- オリジナル CPU を創る
  - 2022 年のセキュリティキャンプ全国大会で RISC-V CPU を自作した
  - 講義では Scala 及び Chisel を使用したものの、応募課題で用いた(System) Verilog-HDL も面白かったため、これを用いた CPU 開発を経験してみたい
    - 幅 8bit、深さ 1024 段の FIFO を System Verilog HDL で作成した（[GitHub Gists](<[https://gist.github.com/watasuke102/da25c45065a55b9a884f99ffa6c4d73c#file-fifo-sv](https://gist.github.com/watasuke102/da25c45065a55b9a884f99ffa6c4d73c#file-fifo-sv)>) にアップロードしています）
  - 既存の 4Bit CPU を 8Bit にアップグレードするという演習も興味がある
    - 昨年作成した CPU は 32Bit だったため、4Bit CPU がどのように作られているかを知らない
    - アップグレードするという過程それ自体が面白そう
- TDD+モブプログラミング
  - 実は今までテストを書いたことがない
  - 設計方法についての知識が全く無いため学んでみたいとずっと思っていて、特に TDD は Twitter 上でフォローしている人が絶賛していたのを見て興味を持っているため、この機会にテストの書き方も含めて学んでみたい
  - セキュリティ・キャンプ 全国大会 2022 に参加した時、成果発表会でモブプログラミングの話を聞いて興味を持った
    - 受講生からの評価も高かったように思えた（詳細はもう覚えていないけれど）
- SimH で PDP-7 ベアメタルプログラミング
  - ハードウェアを直接意のままに触ることが出来るため、ベアメタルプログラミングには興味がある
    - 故に自作 OS にも興味がある
  - 古いマシンの開発手法やアセンブリ等に興味がある
    - 例えばファミコン・PC-98・古い時代のアーケードゲーム……
    - 容量や性能の不足を解決するために用いられた、非常に高い技術力や発想が好き
    - ドラゴンクエスト 3 のプログラミングを行った内藤さんの YouTube チャンネルが好きでたまに見ている
- CanSat をはじめよう
  - ハードウェアの制御経験は学校の授業で H8 マイコンを搭載した車両型ロボットの制御を行ったことのみ
  - 車両型ロボット制御において、センサーをあまり深く触ることが出来なかったので、もっと深くセンサーを制御する開発を行ってみたい
  - チームを組んで開発する経験がほとんど無いため、ここで経験を積みたい
- 競技自作入門
  - もしチームを組んで競技を考えるのであれば、色々な人とルールを考える経験は学校の授業において何度か経験しており、その経験を活かしてみたい
    - そもそも考えること自体が面白かった
      - これはチームでなくても同様だと思っている
    - いくつかの意見を聞いて、共通していそうな部分を抜き出して、双方が納得する折衷案を提案する……というロールを担ったことが何度かあり、今回も可能であれば挑戦したい

</details>
