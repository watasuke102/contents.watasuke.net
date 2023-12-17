---
title: "アセンブリでのsyscallとint 0x80の違い"
tags: ["programming"]
is_favorite: false
published_at: "2021-07-22T09:09:23"
updated_at: "2022-11-27T06:21:36"
---

アセンブリ、難しいですね。正直何も理解しないまま書いてます。

というわけで、今回はタイトル通りです。アセンブリでLinuxのシステムコールを呼び出す時、syscallとint 0x80の違いは何なのかメモしておきます。

## 環境

- nasm 2.15.05
- ld 2.36.1

以下のコードを、`nasm -f elf64 a.s -o a.o && ld -m elf_x86_64 a.o`としてビルドしています。

```nasm
global _start
_start:
  mov		ax, 0x05
  mov		bx, 0x03
  add		ax, bx
  push		ax
  jmp		exit_syscall
exit_int:
  mov		rax, 0x01
  mov		rdi, 0
  int			0x80
exit_syscall:
  mov		rax, 0x3c
  mov		rdi, 0
  syscall
```

レジスタをちょっといじるだけで、なんにもしないプログラムです。このままだとsyscallで、jmpがある行をコメントアウトするとint 0x80で、システムコールを呼び出します。

呼び出すのはsys_exitです。ちなみに、この呼び出しを忘れるとセグフォするので注意しましょう（1敗）。

## 違い

`syscall` はx86_64でカーネルモードに入る（システムコールによって？）方法であるのに対し、`int 0x80`はx86で用いられる方法です。古いから使わないほうが良いらしいです（しらんけど）。

以上の特徴があるため、以下のような違いが出てきます。

### syscallはx86で使えない？

未検証ですが、参考サイトにはこんな感じのことが書いてありました。

### システムコール番号が違う

システムコール番号は/usr/include/asm/unistd\_(32|64).hに記載されています。

今回使ったexitは、x86では0x01番です。それに対し、x86_64は0x3c (60) 番です。  
他にも、例えばwriteなんかは、x86では0x04番、x86_64は0x01 番です。

## おわりに

参考サイトの丸パクリみたいになってしまいました。どうでしょうか。

int 0x80は互換性のために残されているだけで、基本的にsyscallが推奨されている、ということなのかな～とおもいました（こなみ）。

アセンブリぜんぜんわからないのでがんばりたいとおもいます。

## 参考サイト

- [What is better “int 0x80” or “syscall” in 32-bit code on Linux? - StackOverflow](https://stackoverflow.com/questions/12806584/what-is-better-int-0x80-or-syscall-in-32-bit-code-on-linux)
- [x86 Linux の 32bit と 64bit のシステムコールの違い](https://www.mztn.org/lxasm64/x86_x64_table.html)
