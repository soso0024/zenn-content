---
title: "なぜJavaのコードをASTに変換するのか"
emoji: "🤔"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["java", "c", "python", "ast"]
published: true
---

## TL;DR

C 言語と Java, Python の違いについて簡単にまとめました。
下記の論文を読んでいる際に，Java のコードを AST に変換しており，なぜ Java のコードを AST に変換するのか（なぜ他の言語ではないのか）という疑問が湧いたため，調べてみました。
https://arxiv.org/abs/1803.09473

## C 言語、Java、Python の違い

### 1. コンパイルと実行

- C 言語

  - ソースコード(.c) → コンパイル(gcc) → 実行ファイル(.exe/.out)
  - アセンブリ確認可能(-S オプションで.s ファイル生成)

    - アセンブリ言語: 人間が読みやすい形式の機械語

    :::details 例
    下記のコードを `hello.c` として保存し、`gcc -S hello.c` でコンパイルすると、`hello.s` が生成されます。

    ```c
    #include <stdio.h>

    int main()
    {
        printf("Hello World");

        return 0;
    }
    ```

    生成された `hello.s` ファイルは下記のようになります。

    ```s
        .section	__TEXT,__text,regular,pure_instructions
        .build_version macos, 15, 0	sdk_version 15, 1
        .globl	_main                           ; -- Begin function main
        .p2align	2
    _main:                                  ; @main
        .cfi_startproc
    ; %bb.0:
        sub	sp, sp, #32
        stp	x29, x30, [sp, #16]             ; 16-byte Folded Spill
        add	x29, sp, #16
        .cfi_def_cfa w29, 16
        .cfi_offset w30, -8
        .cfi_offset w29, -16
        mov	w8, #0                          ; =0x0
        str	w8, [sp, #8]                    ; 4-byte Folded Spill
        stur	wzr, [x29, #-4]
        adrp	x0, l_.str@PAGE
        add	x0, x0, l_.str@PAGEOFF
        bl	_printf
        ldr	w0, [sp, #8]                    ; 4-byte Folded Reload
        ldp	x29, x30, [sp, #16]             ; 16-byte Folded Reload
        add	sp, sp, #32
        ret
        .cfi_endproc
                                            ; -- End function
        .section	__TEXT,__cstring,cstring_literals
    l_.str:                                 ; @.str
        .asciz	"Hello World"

    .subsections_via_symbols
    ```

  - プラットフォーム依存の実行ファイル生成

- Java

  - ソースコード(.java) → コンパイル(javac) → バイトコード(.class) → JVM で実行
  - バイトコードは中間言語として機能
  - プラットフォーム非依存（Write Once, Run Anywhere）
  - JIT コンパイラによる実行時最適化

- Python
  - インタプリタ型言語（.py ファイルを直接実行）
  - バイトコードにコンパイル(.pyc ファイル)するが任意
  - 実行時に逐次解釈
  - 動的型付けによる柔軟な実行

### 2. 抽象構文木（AST）での利用（おそらく 🤔）

抽象構文木については、下記の記事を参照してください。
https://hireroo.io/journal/tech/abstract-syntax-tree-for-machine-learning

- C 言語

  - 低レベルな言語構造のため、AST の表現が複雑
  - ポインタ操作やメモリ管理の直接制御により、解析が困難
  - プリプロセッサ指令（`#include`, `#define` など）が AST の構築を複雑化

- Java

  - オブジェクト指向の階層構造が AST の表現に適している
  - 静的型付けにより型安全性が保証され解析が容易
  - 言語仕様が明確で一貫性があり、AST の構築と操作が容易
  - メタプログラミングやリフレクションのサポートにより、AST 操作が柔軟

- Python
  - 動的型付けにより型の解析が実行時まで不明確
  - AST 操作は可能だが型安全性の保証が難しい
  - 文法の柔軟性が AST の構造を複雑にする
  - `ast`モジュールで構文解析は可能だが、静的解析ツールの開発は困難

### 3. メモリ管理

- C 言語: 手動（`malloc`/`free`）
- Java: 自動（ガベージコレクション）
- Python: 自動（参照カウント + ガベージコレクション）

### 4. 実行環境

- C 言語: OS やハードウェア上で直接実行
  - コンパイラ言語
- Java: JVM 上で実行
  - コンパイラ言語
- Python: インタプリタ上で実行
  - インタプリタ言語

:::details コンパイラ言語とインタプリタ言語の違い

- コンパイラ言語: プログラムのコードを機械語に変換したものをファイルとして保存しておき（コンパイル）、実行時に呼び出してそのまま処理
- インタプリタ言語: 言語のファイルとして保存しておき、実行時に呼び出して一行ずつ機械語に変換しながらプログラムを実行

![](/images/2024-12-06-diff-c-java/diff_table.jpg)
![](/images/2024-12-06-diff-c-java/diff_flow.jpg)

:::

### 5. パフォーマンス特性

- C 言語: 直接実行で高速だが、プラットフォーム依存
- Java: JVM のオーバーヘッドあり、但し実行時最適化で性能改善
- Python: インタプリタ実行により比較的低速だが、開発効率が高い

### 6. 主な用途

- C 言語: システムプログラミング、組込み開発、高性能アプリケーション
- Java: エンタープライズアプリケーション、Web アプリケーション、クロスプラットフォーム開発
- Python: データ分析、AI/ML、スクリプティング、プロトタイピング

## 参考文献

https://freelance-hub.jp/column/detail/286

https://www.genspark.ai/spark/%E3%82%A4%E3%83%B3%E3%82%BF%E3%83%97%E3%83%AA%E3%82%BF%E3%81%A8%E3%82%B3%E3%83%B3%E3%83%91%E3%82%A4%E3%83%A9%E3%81%AE%E6%AD%B4%E5%8F%B2%E7%9A%84%E7%B5%8C%E7%B7%AF/7b9aa8e3-5a60-491e-bcb9-d528e49f51c0

http://ext-web.edu.sgu.ac.jp/koike/CA14/assembler.html#:~:text=%E3%82%B3%E3%83%B3%E3%83%94%E3%83%A5%E3%83%BC%E3%82%BF%E3%81%AF%E6%95%B0%E5%AD%97%E3%81%AE%E4%B8%A6%E3%81%B3,%E3%82%A2%E3%82%BB%E3%83%B3%E3%83%96%E3%83%AA%E8%A8%80%E8%AA%9E%E3%81%A8%E3%81%84%E3%81%84%E3%81%BE%E3%81%99%E3%80%82
