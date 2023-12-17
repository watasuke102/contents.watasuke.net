---
title: "WSL2でMediaPipeをビルドし、ハンドトラッキングしてみる（失敗）"
tags: ["linux"]
is_favorite: false
published_at: "2021-05-05T08:55:55"
updated_at: "2022-11-27T06:20:52"
---

Googleが公開しているMediaPipeをWindowsのWSL上でビルドして、ハンドトラッキングを実行してみようと思って色々やったのですが、うまくいきませんでした。  
今回は、いちおう方法をまとめておきます。

## 必要な環境

- Windows 10
- WSL2（Xサーバー等でGUIソフトが起動できること）
  今回はAlter Linux on WSLを使用します。
- Androidスマートフォン（カメラ+WiFi環境があること）

## パッケージのインストール

必要なパッケージをインストールします。OpenCV、FFmpeg、Bazel、numpyです。

```bash
$ yay -S opencv ffmpeg bazel python-numpy
```

## ソースコードを持ってくる

結構時間がかかるので、パッケージのインストールと同時進行したほうが良いと思います。

```bash
$ git clone https://github.com/google/mediapipe.git
```

## とりあえずHello Worldをビルド

環境構築ができているかどうか確かめるため、ひとまずhello worldを表示させます。  
さっきクローンしたmediapipeフォルダに移動し、以下のコマンドを実行するだけで終わりです。

参考サイトはUbuntu上で動作させるために `WORKSPACE` を編集してるらしいですが、自分の環境では普通に動作しました。  
Bazelのバージョンが～というエラーが出た場合、フォルダ内の `.bazelversion` をリネームすると動くと思います。~~大丈夫なのかなこれ・・・~~

```bash
$ GLOG_logtostderr=1 bazel run --define MEDIAPIPE_DISABLE_GPU=1 mediapipe/examples/desktop/hello_world:hello_world
```

実行結果は以下の通りです。

```
I20210505 15:42:32.515163  9235 hello_world.cc:57] Hello World!
I20210505 15:42:32.515264  9235 hello_world.cc:57] Hello World!
I20210505 15:42:32.515290  9235 hello_world.cc:57] Hello World!
I20210505 15:42:32.515300  9235 hello_world.cc:57] Hello World!
I20210505 15:42:32.515317  9235 hello_world.cc:57] Hello World!
I20210505 15:42:32.515363  9235 hello_world.cc:57] Hello World!
I20210505 15:42:32.515429  9235 hello_world.cc:57] Hello World!
I20210505 15:42:32.515478  9235 hello_world.cc:57] Hello World!
I20210505 15:42:32.515548  9235 hello_world.cc:57] Hello World!
I20210505 15:42:32.515739  9235 hello_world.cc:57] Hello World!
```

## ハンドトラッキングのサンプルをビルドしてみる

以下のコマンドでビルドできます。

```bash
$ GLOG_logtostderr=1 bazel build -c opt --define MEDIAPIPE_DISABLE_GPU=1 mediapipe/examples/desktop/hand_tracking:hand_tracking_cpu
```

すると、 `opencv2/core/version.hpp: No such file or directory` とエラーが。  
MediaPipeのGitHubリポジトリに[Issue](github.com/google/mediapipe/issues/496) がありました。この問題は `third_party/opencv_linux.BUILD` を編集することで解決しました。

まず、srcsの中に、lib/x86_64-linux-gnu/libopencv～という文字列が並んでいるので、「x86_64-linux-gnu」の部分をすべて削除しました。

次に、hdrsやincludesを以下のように変更します。

```diff
hdrs = glob([
        # For OpenCV 3.x
-      "include/opencv2/**/*.h*",
+      # "include/opencv2/**/*.h*",
        # For OpenCV 4.x
-       # "include/opencv4/opencv2/**/*.h*",
+       "include/opencv4/opencv2/**/*.h*",
    ]),
    includes = [
        # For OpenCV 3.x
-        "include/",
+       # "include/",
        # For OpenCV 4.x
-       #"include/opencv4/",
+       "include/opencv4/",
    ],
```

OpenCV4用の設定を有効化する感じですね。これでビルドできると思います。

ちなみに、ビルド中にWSLが落ちました。これは恐らくメモリ不足らしいです（htopで確認するとメモリ使用量が最大値まで上がっていた）。docker-composeがメモリをバカ食いするのに耐えかねて利用可能なメモリを8GBに制限していたのが原因っぽいですね。  
WSLが応答しなくなってもしばらく待ち続けていれば回復しました。

ビルドが終わったら、起動します。

```bash
$ GLOG_logtostderr=1 bazel-bin/mediapipe/examples/desktop/hand_tracking/hand_tracking_cpu --calculator_graph_config_file=mediapipe/graphs/hand_tracking/hand_tracking_desktop_live.pbtxt
```

さて、恐らくエラーが出ると思います。これはWSL2がカメラをサポートしていないのが原因です。WSL + MediaPipeの記事はWSL1ばっかりだったので、もしかするとWSL2でも対応しているのでは・・・？と思ったのですが、駄目でした。

というわけで、DroidCamを使用して、WSLにAndroidのカメラを接続しようと思います。

## DroidCamのセットアップ

インストールします。

```bash
$ yay -S libappindicator-gtk3 droidcam  v4l2loopback-dc-dkms
```

そして、`sudo modprobe v4l2loopback_dc`すればうまくいくはずだったのですが・・・WSLではv4l2loopbackが機能しないようでした。

ということで、WSLでMediaPipe+カメラでハンドトラッキングはまだ難しいみたいです。

## おわりに

「まだ難しいみたいです」とは書きましたが、一応[USBをWSLで使用する](https://ktkr3d.github.io/2020/07/06/USB-support-to-WSL2/) 事もできるらしいので、100%不可能ではないような気もします。カーネルビルドしなきゃいけないのでやりませんでしたが。

ということで、おとなしく実機のArch Linuxを使います・・・。

## 参考サイト

- [【MediaPipe】Windowsで環境構築し、Multi Hand Trackingを動かしてみた](https://dev.classmethod.jp/articles/mediapipe-environment-setting-windows/)
- [DroidcamをArchLinux上で使う](https://qiita.com/Hayao0819/items/2c9ed47ab7f1e7a3fb62)
- [https://scienceboy.jp/88io/2020/05/droidcam/](https://scienceboy.jp/88io/2020/05/droidcam/)
