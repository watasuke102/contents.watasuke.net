---
title: "OpenSiv3D 0.6.3をArch Linuxでビルドする"
tags: ["linux", "programming"]
is_favorite: false
published_at: "2022-04-04T11:08:30"
updated_at: "2022-04-04T11:08:30"
---

以前のブログで、こんな記事を書きました。

http://menster.wp.xdomain.jp/opensiv3d-linux/

ただ、この記事はかなり情報が古くなっています。まず記事を書いてしばらく経った頃にv0.4.3がリリースされ、このバージョンでAngelScriptの別途インストールが不要になりました。これについては記事内でも補足しています。また、v0.6.0のリリースに伴って、ビルド方法がかなり変更されたらしいです（詳細はよくわかってないですが）。

ということで、最近インストールしたばかりのArch LinuxにOpenSiv3D 0.6.3開発環境を作ってみたので、やり方をざっくりまとめます。

## 環境

- Arch Linux + i3wm
- Ryzen 5 2600
- GTX 1060 6GB

## 依存パッケージのインストール

とりあえずこれからです。以下のコマンドで必要なパッケージのほとんどをインストールできます。

パッケージが足りなかったり余分だったりしたら教えてください。

```bash
sudo pacman -S git cmake ninja alsa-lib ffmpeg4.4 gtk3 libcgif glu harfbuzz mpg123 opencv opus opusfile soundtouch libtiff libjpeg-turbo libvorbis libwebp libxft
```

さて、OpenSiv3Dはboostに依存しており、バージョンもはっきり指定されています。執筆時点でのArchリポジトリにあるboostのバージョンは1.78.0ですが、0.6.3時点の要求バージョン（READMEに書いてある）は**「Boost 1.71.0 - 1.73.0」**です。ということで、**1.74.0**を入れます。何を言っているのかわからないと思いますが、アーカイブに1.73.0は無く、1.72.0ではビルドできません。だから、1.74.0を入れる必要があったんですね。多分ちゃんとビルドできるので安心してください。

古いバージョンのパッケージは、[Arch Linux Archive](https://archive.archlinux.org/)に置いてあります。boostは以下のページにあります。

https://archive.archlinux.org/packages/b/boost/

この中から1.74.0を探して、最後の拡張子がsigでないものをダウンロードします。今回ダウンロードしたファイル名は「boost-1.74.0-2-x86_64.pkg.tar.zst」です。

また、バージョンを合わせてboost-libsもダウンロードします。以下から探してください。今回は「boost-libs-1.74.0-2-x86_64.pkg.tar.zst」でした。

https://archive.archlinux.org/packages/b/boost-libs/

ダウンロードしたら、それら2つを同じディレクトリに配置し、そのディレクトリで以下のコマンドを実行してインストールします。

```bash
$ sudo pacman -U <name>
```

<name>はダウンロードしたファイルの名前に適宜変更してください（2つあるので、スペースで区切る）。今回であれば `sudo pacman -U boost-libs-1.74.0-2-x86_64.pkg.tar.zst boost-1.74.0-2-x86_64.pkg.tar.zst` になると思います。

## ソースコードのダウンロードと編集

とりあえず、GitHubからソースコードをダウンロードします。

```bash
$ git clone https://github.com/Siv3D/opensiv3d
```

そうしたら、以下の編集を行ってください。

- `src/Siv3D-Platform/Linux/Siv3D/AudioCodec/CAudioCodec.cpp` の17行あたり、extern "C"の内部に`# include <libavcodec/avcodec.h>`という文を貼り付ける
- `src/ThirdParty/cpptoml/cpptoml.h` の序盤あたりに、`#include <limits>` という文を貼り付ける

以上で編集は終わりです。

## ビルドとサンプルプログラムの実行

まずLinux用ライブラリのビルドディレクトリを作成し、cmakeでビルドします。以下のコマンド群を一行ずつ実行してください。

```bash
$ cd opensiv3d/Linux
$ mkdir build && cd build
$ PKG_CONFIG_PATH=/usr/lib/ffmpeg4.4/pkgconfig cmake -GNinja ..
$ ninja
```

なかなか時間がかかりますが、完了すると `libSiv3D.a` が生成されます。されてなかったら何か間違ってる可能性があります。

そのままサンプルプログラムを実行しましょう。Linux版のサンプルプログラムは、CIの都合上起動しても何もせず終了してしまうらしいので、Windowsのサンプルプログラムを持ってきてビルドしてみます。

```bash
$ cd ../App
$ cp ../../WindowsDesktop/Main.cpp .
$ mkdir build && cd build
$ cmake -GNinja ..
$ ninja
```

そうすると、ひとつ上のフォルダ（Linux/App/）に、Siv3DTestが生成されます。実行してみて、ちゃんと動くかどうか確かめてみてください。

https://twitter.com/Watasuke102/status/1510858296055382020

ということで、ここからはMain.cppを編集したりして開発を進めていけばOKです。

## 余談

↑のツイートをした直後に色々修正されたり、最新のソースではもとから修正されてたりしてたらしいです。OpenSiv3D 0.6.4が公開されたらソースコードの編集やffmpegのバージョンを変える必要はないとのことです。

https://twitter.com/Reputeless/status/1510865593045757954
