---
title: "HTML+CSSで任意角度の弧を描画する"
tags: [""]
is_favorite: false
published_at: "2023-02-23T06:56:20"
updated_at: "2023-02-23T07:10:13"
---

## 結論

scssを使っている場合、

```scss
@mixin fan($color, $size, $border_width, $length, $offset) {
  border-radius: 50%;
  width: $size;
  height: $size;

  $degree: calc(#{$length} + #{$offset});
  background: conic-gradient(
    transparent $offset,
    $color $offset,
    $color $degree,
    transparent $degree
  );
  $hole_radius: calc(#{$radius} / 2 - #{$border_width});
  -webkit-mask: radial-gradient(
    circle at center,
    transparent $hole_radius,
    black $hole_radius,
    black
  );
}
```

これをincludeしましょう。以下のようになります（これをincludeした要素を4つ表示しています）。

https://twitter.com/Watasuke102/status/1628638930890534912

こんな感じで試すことが出来ます。普通のcssを使ってる人はここからコピペしてください。

```html
<!doctype html>
<html>
  <head>
    <style>
      .fan {
        border-radius: 50%;
        width: 300px;
        height: 300px;

        background: conic-gradient(
          transparent 20deg,
          #000 20deg,
          #000 180deg,
          transparent 180deg
        );
        -webkit-mask: radial-gradient(
          circle at center,
          transparent calc(300px / 2 - 4px),
          white calc(300px / 2 - 4px),
          white
        );
      }
    </style>
  </head>
  <body>
    <div class="fan"></div>
  </body>
</html>
```

`#000` は線の色、 `300px`は直径、 `20deg` は弧のオフセット（開始角度）、`180deg`は弧の長さ、`4px` は弧の太さです。自由にいじってください。

## 経緯

現在ポートフォリオをいじっています。正直今のポートフォリオは情報も少ないし微妙なデザインだしで救いようがないので、もっと見栄えの良いものにしようと思って奮闘中です。

ということで、動画を作りました。アニメーションのあるデザインはAviUtlで作るとかなり良いのでおすすめです。作ってる過程で自分の作りたい物がかなり明確になった気がします。

https://twitter.com/Watasuke102/status/1628561460422144000

ただ、これを実装するのは簡単ではなくて、特に弧を描く部分はかなり難航しました。角度や半径を自由に設定できるようにするために色々と試行錯誤する羽目になりました。最終的に出来たのは以下のようなものです。

https://twitter.com/Watasuke102/status/1628615588531806210

ということで、今回はこれの作り方を解説します。まあ最初に貼ったコードをコピペすれば動くはずですが。

## つくりかた

まずは円形にします。

```scss
border-radius: 50%;
width: $size;
height: $size;
```

そして、`conic-gradient` を用いて、背景の一部を透明にします。

```scss
background: conic-gradient(
  transparent $offset,
  $color $offset,
  $color $degree,
  transparent $degree
);
```

これは名前の通り普通に使えばグラデーションになります。グラデーションを回避する方法はMDNに載っています。

> 2 つ以上の色経由点が同じ場所にある場合、その場所で宣言された最初の色と最後の色の間に不連続の (硬い) 色変化として表示されます。扇形グラデーションを使用して円グラフを作成するには、 -- 背景画像はアクセシビリティが確保できないため、正しい方法ではありませんが -- 隣接する 2 つの色経由点の色経由点の角度が同じである、硬い色経由点を使用します。これを実現する最も簡単な方法は、複数のポジションの色経由点を使用することです

ちょっと難しい言い回しですが、まあ `transparent $offset, #000 $offset,` というように、同じ角度において2回色を指定すれば良いということです。サンプルは以下の通り（上記の引用もこのリンクより少し上のほうからです）で、うまく使えば円グラフが作れることがわかります。

https://developer.mozilla.org/ja/docs/Web/CSS/gradient/conic-gradient#gradient_pie-chart

ここで、`$degree` は `calc(#{$length} + #{$offset})` と算出できます。0°～offsetまでを透明に、そこからlength分の色を指定し、残りはすべて透明、という感じに指定しています。なんで `#{$hoge}` にしなければいけないのかは正直よくわかっていません。

最後に、maskを用いて中を切り抜きます。

```scss
-webkit-mask: radial-gradient(
  circle at center,
  transparent $hole_radius,
  white $hole_radius,
  white
);
```

中央から円形に広がるグラデーションをマスクとしています。今回もグラデーションは不要なので、前述の通りに同じ点で色をふたつ指定することによって回避しています。

https://developer.mozilla.org/ja/docs/Web/CSS/gradient/radial-gradient

`$hole_radius` は `calc(#{$radius} / 2 - #{$border_width})` と算出します。`$size` は直径だから2で割って半径を算出し、そこから枠線の太さだけ小さくした部分を透明にするという処理です。

この記事を書いた時点では `-webkit-mask` じゃないと動きませんでした。また、そんなに多くのパターンを試したわけではないですが、`white` の部分はおそらくどの色でも良いと思います。blackやredでも動きます。

## おわりに

scssサイコ～～～～！！

CSSを書いてるって感じがして結構楽しかったです。
