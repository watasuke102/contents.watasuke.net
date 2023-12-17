---
title: "WindowsでインストールしたAPEXをArch Linuxからプレイする"
tags: ["linux", "game"]
is_favorite: false
published_at: "2022-03-24T12:25:53"
updated_at: "2022-03-24T12:49:21"
---

https://twitter.com/Watasuke102/status/1506910930772193282

**Arch Linuxで！！！！！APEX！！！！！**

ということで、ついにAPEXをLinux上で動かせるようになりました。前から囁かれていたので興味はあったのですが、Arch Linuxをちゃんとデスクトップ PCにインストールしたこのタイミングで、Windows用Steamからインストールしたゲームファイルを大体そのまま動かせると知って、これはやるしかない！！！と思ったのでやってみました。やり方を書いておきます。

## 環境

細かいとこはneofetchを見てください・・・

https://twitter.com/Watasuke102/status/1506093120961716227

- Arch Linux（ext4）、Windows 11（NTFS）
- zenカーネル
- GTX1060 6GB
- Arch Linuxにおいて、nvidia-dkmsをインストール済
- Windowsにおいて、SteamがインストールされているパーティションがNTFSである

## インストールするもの

まず、multilibを有効化します（Steamをインストールするのに必要なため）。/etc/pacman.confを開いてmultilib関連の行をアンコメントするだけです。

そして、Steamと、NTFSパーティションのマウント用にntfs-3gをインストールします。

```
$ pacman -S steam ntfs-3g
```

AURにあるprotonをインストールする必要はありません。

## protonを有効化

Steamを開き、上部メニューからSetting→Steam Play→Enable Steam Play for all other titlesにチェックを入れます。Run other titles withはProton Experimentalを選びました。まあSteam Play項目のチェックボックスに全部入れればいいと思います。

これを選んだら多分再起動を要求されると思います。再起動せずに普通に終了させましょう。

## Windows用パーティションをマウントする

次に、WindowsでインストールしたゲームファイルをLinuxから読み込めるようにします。そのために、OSの起動時にパーティションを自動マウントできるようにしていきます。ただし、普通にやるとマウントポイントのオーナー設定がrootになってしまい、ゲームが起動しなくなってしまいます（1敗）。ということで、単純にgenfstabするだけでは駄目なので、直接fstabを編集します。

まず、Windows上でSteamをインストールしたパーティションを任意の場所にマウントします。私の場合はE:\Steamにインストールしていて、このパーティションはLinux上では/dev/nvme0n1p1だったので、マウント用のコマンドは

```
$ sudo mount /dev/nvme0n1p1 ~/game
```

となりました。このマウントポイントはどこでもいいですが、場所はちゃんと覚えておいてください。

次に、これをfstabに記録します。記録するにあたって、uidとgidが必要なので、idコマンドで調べてメモしておいて下さい。
/etc/fstabを開き、以下のように追記します。

```
/dev/nvme0n1p1    /home/watasuke/game    ntfs-3g    uid=<uid>,gid=<gid>,umask=0022    0    0
```

`/dev/nvme0n1p1`は先ほどマウントしたパーティション、`/home/watasuke/game`はマウントポイント、`<uid>`と`<gid>`は先ほど調べたものに置き換えてください。

保存したら再起動しましょう。

## シンボリックリンクを貼る

Linuxにおいて、Steamはデフォルトで`~/.steam/root/steamapps`に
ゲームをインストールします。ということで、ここにWindowsのsteamappsディレクトリのシンボリックリンクを貼れば解決です。

```
$ ln -rs ~/game/Steam/steamapps ~/.steam/root/steamapps
```

前半の`~/game/Steam/steamapps`は各自で書き換えてください。

そしてSteamを起動し、APEXを起動してみましょう。

## シェーダーキャッシュと垂直同期

もし、「起動はするけど若干カクつく」という症状が出た場合は、以下の作業を試してみてください。

### 垂直同期を切る

APEX内の設定→ビデオ→垂直同期をオフに設定します。

### シェーダーキャッシュの導入

ファイルをダウンロードし、特定の場所に配置します

1. [ここ](https://cdn.discordapp.com/attachments/483946408676818974/953763386536132658/r5apex.dxvk-cache)からファイルを「r5apex.dxvk-cache」という名前でダウンロード
2. `.steam/root/steamapps/shadercache/1172470/DXVK_state_cache/r5apex.dxvk-cache`としてコピー

（Redditで紹介されてました）

https://www.reddit.com/r/linux_gaming/comments/t5xrho/dxvk_state_cache_for_fixing_stutter_in_apex/

自分の場合は先にシェーダーキャッシュを導入して変わらず、最終的に垂直同期を切る方で解決しました。

## おわりに

GPUのドライバやLinuxカーネルによって作業が変わるかもしれませんが、とりあえず自分の場合はこの方法でプレイすることができました。

ファイルのオーナー設定には気をつけよう！
