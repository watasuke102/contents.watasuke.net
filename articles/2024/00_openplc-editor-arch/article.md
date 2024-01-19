---
title:        'OpenPLC EditorをArch Linux上で動かす'
tags:         ['linux']
is_favorite:  false
published_at: '2024-01-09T15:33:01'
updated_at:   '2024-01-20T01:35:16'
---

# 2024-01-20追記

Python2の導入がダルくてつらいよ～と思って書いたこの記事ですが、どうやら別ブランチかどこかでPython3への移行が進められていたらしく、現在のmasterブランチはPython3.8に対応したものになっています。

ということで、 `yay -S openplc-editor-git` で終わりです。以下の記事は記録として残しておきますが、たぶん何の意味もないです。

---

僕の学科では現在、論理回路という授業が行われており、これは名前の通り回路について学ぶ授業です。この授業においては、論理回路のシミュレーションとしてOpenPLC Editorを用いています。

ところで、冬休みに入る直前ごろにOpenPLC Editorについて調べた時、エディタのダウンロードページが見当たりませんでした。[Gitlab](https://gitlab.com/openplcproject/openplc_v3)も更新が止まっていて厳しいなあと思っていたのですが、冬休みが明けるころに見たら何やらモダンなWebサイトがヒットしました。

https://autonomylogic.com/

どうも[GitHub](https://github.com/thiagoralves/OpenPLC_Editor)のほうにリポジトリがあって、そちらで開発が行われているようです。しかも12月末にコミットが発生していました。

これは導入するしかない！と思って入れようとしたところ、かなり難航してしまったので、ここにまとめておきます。

## 検証した環境

Arch Linux x86_64、カーネルは6.6.9-zen-1-1-zenです。

## 必要なパッケージのインストール

まずはパッケージをインストールしましょう。なお、依存関係であるにもかかわらず、ここに書いていないものがあるかもしれません。

```sh
$ sudo pacman -S llvm clang pyenv --needed
```

## Python環境の構築

まずはPython2を導入します（つらい）。pyenvを使用します[^pymanager]。

[^pymanager]: Python2が使えたらなんでもいいとは思いますが、責任は取りません。ここではpyenvを使用したことを前提にして説明します

```sh
$ pyenv install 2.7.18
$ pyenv shell 2.7.18
```

そして、依存関係**の一部**をpipで入れます。

```sh
$ pip2 install future zeroconf==0.19.1 numpy==1.16.5 matplotlib==2.0.2 lxml==4.6.2 pyro sslpsk pyserial jinja2
```

## ld.so.confの設定

このままだと、後述するwxPython3のビルド中に `libpython2.7.so.1.0` が見つからないという怒られが発生するので、以下のようにして認識させておきます。

```sh
$ echo "/home/${USER}/.pyenv/versions/2.7.18/lib" | sudo tee /etc/ld.so.conf.d/python2.conf
$ sudo ldconfig
```

`ldconfig -p | grep python2` などで、`libpython2.7.so.1.0` がldconfigによって認識されていることを確認してください。

## wxPython3の導入

OpenPLC EditorはwxPython3に依存しています。これは古いバージョンであり、pipで導入できません。

ということでAURから `python2-wxpython3` をインストール……したかったのですが、手元の環境だとエラーが出てしまいました。

というわけで、PKGBUILDを以下のページから拾ってきて、改変しましょう。

https://aur.archlinux.org/cgit/aur.git/tree/PKGBUILD?h=python2-wxpython3

これらの箇所を修正します。

- `build()` の最初に以下を追記（`USER` はユーザー名に置き換えてください）
```
export LD_LIBRARY_PATH=/home/USER/.pyenv/versions/2.7.18/lib
export CC=clang
export CXX=clang++
```
- `build()` において、`CFLAGS` に `-std=${_C_std}` を渡しているところを削除
- `package()` で `${pkgdir}/usr/bin/*` に対してforしている行を削除

<details>
<summary>最終的に以下のようになると思います。</summary>

```bash
  # 前半略

build() {
  # added:
  export LD_LIBRARY_PATH=/usr/local/lib:/home/watasuke/.pyenv/versions/2.7.18/lib
  export  CC="clang"
  export CXX="clang++"
  # Old, non-maintained software. Use older C/C++ standard.
  _C_std='gnu++14'
  _CXX_std='gnu++14'

#In case a newer C standard is used, warnings are generated which should not be treated as errors.
_append_to_CFLAGS_after_configure=' -Wno-error -Wno-error=format-security -Wno-error=register -Wno-error=deprecated-declarations -Wno-error=alloc-size-larger-than= -Wno-error=write-strings -Wno-error=return-local-addr -Wno-error=attributes' # Or just use -Wno-error to catch all.
_append_to_CXXFLAGS_after_configure="${_append_to_CFLAGS_after_configure}"

  # changed:
  # CFLAGS+=" -std=${_C_std}"
  CFLAGS+="-O0"
  CXXFLAGS+="-std=${_CXX_std} -O0"
  export CFLAGS
  export CXXFLAGS

    cd wxPython-src-${pkgver}
    ./configure "${_configure_opts[@]}" \
        --prefix=/usr \
        --libdir=/usr/lib \
        --includedir=/usr/include \
        --with-gtk=3 \
        --with-opengl \
        --enable-unicode \
        --enable-graphics_ctx \
        --disable-precomp-headers \
        --with-regex=sys \
        --with-libpng=sys \
        --with-libxpm=sys \
        --with-libjpeg=sys \
        --with-libtiff=sys \
        --with-wx-config=/opt/wxgtk-3.0/bin/wx-config-gtk3

  echo "CONFIG DONE"
  # changed:
  # CFLAGS+=" ${_append_to_CFLAGS_after_configure} -std=${_C_std}"
  CFLAGS+=" ${_append_to_CFLAGS_after_configure}"
  CXXFLAGS+=" ${_append_to_CXXFLAGS_after_configure} -std=${_CXX_std} "
  export CFLAGS
  export CXXFLAGS

  make

  cd wxPython
  python2 setup.py WX_CONFIG=/opt/wxgtk-3.0/bin/wx-config-gtk3 WXPORT=gtk3 UNICODE=1 build
}

package() {
    cd "wxPython-src-${pkgver}/wxPython"
    python2 setup.py WX_CONFIG=/opt/wxgtk-3.0/bin/wx-config-gtk3 WXPORT=gtk3 UNICODE=1 install --root="${pkgdir}" --optimize=1

    install -d "${pkgdir}"/usr/include/wx-3.0/wx/wxPython
    cp -r "${pkgdir}"/opt/wxgtk-3.0/include/wx-3.0/wx/wxPython/* "${pkgdir}"/usr/include/wx-3.0/wx/wxPython
    rm -r "${pkgdir}"/opt

    # deleted:
    # for file in "${pkgdir}"/usr/bin/*; do mv "${file}" "${file}2"; done
    install -Dm644 ../docs/licence.txt "${pkgdir}"/usr/share/licenses/${pkgname}/LICENSE
}
```

</details>

`makepkg -si` してインストールしましょう。

## OpenPLC Editorのビルド

以下のリポジトリをcloneしてディレクトリに入り、以下のようにコマンドを打っていけば良いです。基本的にリポジトリに含まれる `install.sh` に沿っていますが、configureで `CXXFLAGS` を指定しているという差異があります。

```sh
$ cd matiec
$ autoreconf -i
$ ./configure CXXFLAGS="-std=c++03"
$ make
$ cp ./iec2c ../editor/arduino/bin
$ cd ..
$ echo -e "#!/bin/bash\n\
cd \"$(pwd)\"\n\
if [ -d \"./new_editor\" ]\n\
then\n\
    rm -Rf editor\n\
    rm -Rf ./matiec/lib\n\
    mv ./new_editor ./editor\n\
    mv ./new_lib ./matiec/lib\n\
fi\n\
python2.7 ./editor/Beremiz.py" > openplc_editor.sh
$ chmod +x openplc_editor.sh
```

<details>
<summary>もしくは、このようなPKGBUILDを作ってmakepkg -siしても良いと思います。</summary>

```sh
#Maintainer: watasuke <watasuke102@gmail.com>

_pkgname=openplc-editor
pkgname="${_pkgname}-git"
pkgver=r439.7b11e38
pkgrel=1
pkgdesc="OpenPLC editor"
arch=("x86_64")
url="https://github.com/thiagoralves/OpenPLC_Editor"
source=("git+${url}.git")
license=("GPL3")
md5sums=("SKIP")
makedepends=("git")

pkgver() {
  cd "${srcdir}/OpenPLC_Editor"
  printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

build() {
  cd "${srcdir}/OpenPLC_Editor"
  cd matiec
  autoreconf -i
  ./configure --prefix="${pkgdir}" CXXFLAGS="-std=c++03"
  make
  cp ./iec2c ../editor/arduino/bin
}

prepare() {
  cd "${srcdir}/OpenPLC_Editor"
  echo -e "#!/bin/bash\n\
cd \"/opt/OpenPLC_Editor\"\n\
if [ -d \"./new_editor\" ]\n\
then\n\
    rm -Rf editor\n\
    rm -Rf ./matiec/lib\n\
    mv ./new_editor ./editor\n\
    mv ./new_lib ./matiec/lib\n\
fi\n\
python2.7 ./editor/Beremiz.py" > openplc_editor.sh
  chmod +x openplc_editor.sh
}

package() {
  install -dm755 "$pkgdir/opt"
  cp -r "${srcdir}/OpenPLC_Editor" "$pkgdir/opt/OpenPLC_Editor"
}
```

</details>

## 実行

先程作成した `openplc_editor.sh` を実行すると、OpenPLC Editorが起動します。多分。PKGBUILDから入れた人は `/opt/OpenPLC_Editor/openplc_editor.sh` です。

![OpenPLC Editor on Hyprland](/img/openplc-editor.png)

Waylandでも動きます（画像はHyprland）。うれしい！

## おわりに

Arch LinuxでPython2とwxPython3を導入するのがここまでつらいとは思っていませんでした……。特にwxPython3のビルドは大変でした。OpenPLC Editorのリポジトリに含まれる.deb形式のパッケージをインストールしてみたりもしたのですが、うまく動きませんでした。

というわけで、いろいろ試行錯誤していたため、なにか書き忘れていることがあるような気がしてなりません。何かあったら教えてください。

まあ何はともあれ、起動できるようになってよかったです。OpenPLC Editorがインストールされている学校のパソコンを、授業がある度に借りる必要があったのですが、これのおかげで必要なくなりそうで嬉しいです。

---

ちなみに：PKGBUILDがいくつか出ていますが、これをAURに公開するつもりはあんまりないです。pyenvの環境設定という前提があるので……。Nix(OS)ならこういうの楽そうですよね。