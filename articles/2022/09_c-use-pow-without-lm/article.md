---
title: "【C言語】ライブラリをリンクしなくてもpowが使える（最適化オプションについて調べた話）"
tags: ["programming", "c-or-cpp"]
is_favorite: false
published_at: "2022-08-07T05:08:37"
updated_at: "2022-08-07T06:32:01"
---

pow関数をご存知ですか？math.hをインクルードすることによって使えるようになる、第1引数の第2引数乗を得ることが出来る数学関数です。ちなみにC17では7.12.7.4に書いてあります。

さて、math.hといえば、ビルド時に `-lm` といったオプションを付けていなくて怒られた記憶がある人も多いのではないでしょうか。数学に関する関数を呼ぶ時は別途ライブラリのリンクが必要になるということですね。

このオプションを付けないと、こんな感じでリンクエラーになります。

```
$ bat a.c
#include <stdio.h>
#include <math.h>

int main(void) {
  int a;
  scanf("%d", &a);
  double b = pow(a, 2);
  printf("%lf\n", b);
  return 0;
}

$ gcc -g a.c
/usr/sbin/ld: /tmp/ccTfoaH6.o: in function `main':
a.c:(.text+0x54): undefined reference to `pow'
collect2: error: ld returned 1 exit status
```

さて。こんなことを言っておきながら、pow関数を `-lm` オプションなしで使うこともできます。今回はこの現象について書いていきます。

## 最適化オプション

タイトルでネタバレをしていますが、最適化オプションを付けることによって、問題なくビルドできます。

```
$ gcc -g -O a.c
（エラーはもちろん、警告も表示されない）
```

当然、最適化によってpow関数を呼び出す必要がなくなったということでしょう。

## なぜリンクする必要がなくなったのか？

`-O` オプションを付けずにビルドした実行ファイルをobjdumpで覗いてみましょう。

```
$ gcc -g -lm b.c
$ objdump -Mintel -dS a.out
  double b = pow(a, 2);
    119b:       8b 45 ec                mov    eax,DWORD PTR [rbp-0x14]
    119e:       66 0f ef d2             pxor   xmm2,xmm2
    11a2:       f2 0f 2a d0             cvtsi2sd xmm2,eax
    11a6:       66 48 0f 7e d0          movq   rax,xmm2
    11ab:       f2 0f 10 05 5d 0e 00    movsd  xmm0,QWORD PTR [rip+0xe5d]        # 2010 <_IO_stdin_used+0x10>
    11b2:       00
    11b3:       66 0f 28 c8             movapd xmm1,xmm0
    11b7:       66 48 0f 6e c0          movq   xmm0,rax
    11bc:       e8 6f fe ff ff          call   1030 <pow@plt>
    11c1:       66 48 0f 7e c0          movq   rax,xmm0
    11c6:       48 89 45 f0             mov    QWORD PTR [rbp-0x10],rax
```

11bcでpow@pltを読んでいますね。

次に、 `-O` オプションを付けてみましょう。

```
  double b = pow(a, 2);
    117e:       66 0f ef c0             pxor   xmm0,xmm0
    1182:       f2 0f 2a 44 24 04       cvtsi2sd xmm0,DWORD PTR [rsp+0x4]
    1188:       f2 0f 59 c0             mulsd  xmm0,xmm0
```

`[rsp+0x4]` 、すなわち変数aの値を cvtsi2sd (ConVerT Signed-Int to Signed-Double?) で xmm0 に入れ、xmm0 と xmm0 を掛け算しています。つまりa\*aですね。まごうことなきpow(a, 2) です。

つまり、最適化オプションによって、pow(a, 2) は a\*a に展開されることがわかりました。

## -Oオプションは何をする？

gccのドキュメントを読んでみましょう。

https://gcc.gnu.org/onlinedocs/gcc/Optimize-Options.html

以下の最適化フラグが有効になるらしいです。

```
-fauto-inc-dec
-fbranch-count-reg
-fcombine-stack-adjustments
-fcompare-elim
-fcprop-registers
-fdce
-fdefer-pop
-fdelayed-branch
-fdse
-fforward-propagate
-fguess-branch-probability
-fif-conversion
-fif-conversion2
-finline-functions-called-once
-fipa-modref
-fipa-profile
-fipa-pure-const
-fipa-reference
-fipa-reference-addressable
-fmerge-constants
-fmove-loop-invariants
-fmove-loop-stores
-fomit-frame-pointer
-freorder-blocks
-fshrink-wrap
-fshrink-wrap-separate
-fsplit-wide-types
-fssa-backprop
-fssa-phiopt
-ftree-bit-ccp
-ftree-ccp
-ftree-ch
-ftree-coalesce-vars
-ftree-copy-prop
-ftree-dce
-ftree-dominator-opts
-ftree-dse
-ftree-forwprop
-ftree-fre
-ftree-phiprop
-ftree-pta
-ftree-scev-cprop
-ftree-sink
-ftree-slsr
-ftree-sra
-ftree-ter
-funit-at-a-time
```

それでは、-Oで有効になるフラグのうち、pow関数の展開に働きかけるフラグはどれなのか調べてみようと思っていました。

これらをすべて指定してビルドしてみます。

```
$ gcc -g -fauto-inc-dec -fbranch-count-reg -fcombine-stack-adjustments -fcompare-elim -fcprop-registers -fdce -fdefer-pop -fdelayed-branch -fdse -fforward-propagate -fguess-branch-probability -fif-conversion -fif-conversion2 -finline-functions-called-once -fipa-modref -fipa-profile -fipa-pure-const -fipa-reference -fipa-reference-addressable -fmerge-constants -fmove-loop-invariants -fmove-loop-stores -fomit-frame-pointer -freorder-blocks -fshrink-wrap -fshrink-wrap-separate -fsplit-wide-types -fssa-backprop -fssa-phiopt -ftree-bit-ccp -ftree-ccp -ftree-ch -ftree-coalesce-vars -ftree-copy-prop -ftree-dce -ftree-dominator-opts -ftree-dse -ftree-forwprop -ftree-fre -ftree-phiprop -ftree-pta -ftree-scev-cprop -ftree-sink -ftree-slsr -ftree-sra -ftree-ter -funit-at-a-time b.c
```

すると、なんとエラーになってしまいました。

```
cc1: warning: this target machine does not have delayed branches
/usr/sbin/ld: /tmp/ccQTAQOO.o: in function `main':
/home/watasuke/program/c/b.c:7: undefined reference to `pow'
collect2: error: ld returned 1 exit status
```

は～？

その他オプションはそのまま、 `-c` でコンパイルだけしてobjdumpしてみます（コマンド省略）。

```
  double b = pow(a, 2);
  30:   f2 0f 10 05 00 00 00    movsd  xmm0,QWORD PTR [rip+0x0]        # 38 <main+0x38>
  37:   00
  38:   66 0f ef d2             pxor   xmm2,xmm2
  3c:   f2 0f 2a 54 24 0c       cvtsi2sd xmm2,DWORD PTR [rsp+0xc]
  42:   66 48 0f 7e d0          movq   rax,xmm2
  47:   66 0f 28 c8             movapd xmm1,xmm0
  4b:   66 0f 28 c2             movapd xmm0,xmm2
  4f:   e8 00 00 00 00          call   54 <main+0x54>
  54:   66 48 0f 7e c0          movq   rax,xmm0
  59:   f2 0f 11 44 24 10       movsd  QWORD PTR [rsp+0x10],xmm0
```

全然展開されてないですね・・・。

## すべての最適化がフラグによって管理されているわけではない

さっきのgccドキュメントに戻ってもう一回読んでみましょう。

```
Not all optimizations are controlled directly by a flag. Only optimizations that have a flag are listed in this section.
```

ふーん

どうやら、すべての最適化がフラグによって管理されているわけではないらしいです。`-O1` オプションでのみ有効になる最適化によってpow関数が展開されているのでしょうか・・・。

## おわり

調べてみましたが、よくわかりませんでした。
いかがでしたか！？？！？！？

久しぶりに微妙なブログを書いてしまった気がします。Scrapboxでよかったかも。
