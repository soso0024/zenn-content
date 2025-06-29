---
title: "TF-IDFにおけるDF（文書頻度: Document Frequency)の対数を取る理由"
emoji: "🧌"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["TF-IDF", "自然言語処理", "情報検索"]
publication_name: nislab
published: true
---

## TL;DR

TF-IDF における DF（文書頻度: Document Frequency) の対数を取る理由について気になって調べたので、その理由をまとめました。

## 私たちの研究室(NISLab)

https://nisk.doshisha.ac.jp/

## アドベントカレンダー 2日目~

https://nislab-advent-calendar-2024-12.vercel.app/

## 1. TF-IDF の基本的な目的

TF-IDF（Term Frequency-Inverse Document Frequency）は、文書中の単語の重要度を測る指標です。

- **TF**: 単語がその文書内でどれだけ頻出しているか（ローカルな重要度）。
- **IDF**: 単語が全体の文書集合でどれだけ希少か（グローバルな重要度）。

IDF の部分では、希少な単語（特定の文書に特徴的）が高いスコアを持つように調整します。一方、全ての文書で広く出現する単語（例: "the" や "is"）のスコアを低くします。

詳しくは以下のリンクを参照してください。
https://ja.wikipedia.org/wiki/Tf-idf
https://qiita.com/ynakayama/items/300460aa718363abc85c

## 2. IDF における対数の式

IDF は次のように定義されます：

$$
\text{IDF}(t) = \log \frac{N}{DF(t)} + 1
$$

ここで：

- $N$: 全文書の数
- $DF(t)$: 単語 $t$ を含む文書の数（文書頻度）

対数を取る理由には以下のような目的があります。

## 3. 対数を取る理由

### (1) 値のスケーリング（極端な値を抑える）

- $\frac{N}{DF(t)}$ は非常に大きな値になることがあります。例えば、ある単語が極端に希少で、$DF(t)$ が 1 の場合、$\frac{N}{DF(t)}$ は $N$ と等しくなり、スコアが極端に大きくなります。
- 対数を取ることで値の増加が緩やかになり、極端なスコアの偏りを抑えます。

例:

- $N = 1000$, $DF(t) = 1$ の場合：$\frac{N}{DF(t)} = 1000$, $\log(1000) = 3$
- $DF(t) = 10$ の場合：$\frac{N}{DF(t)} = 100$, $\log(100) = 2$

このように、対数を取ることでスコアのスケールが圧縮され、計算結果が安定します。

### (2) スコアの非線形性が自然言語の性質に適合

- 自然言語では、単語の出現頻度と重要度の関係は非線形的です。
- 対数を取ることで、人間が感じる「単語の重要性」の感覚に近いスコアが得られます。希少な単語ほど重要度が急激に増す一方で、ある程度頻出する単語については重要度の増加が抑えられます。

### (3) 数学的な安定性

- $\frac{N}{DF(t)}$ が非常に大きい値をとると、計算精度の問題が生じる場合があります。対数を取ることで、スケールを抑えて計算を安定させます。

### (4) 小さな値への対応（+1 の役割）

- $DF(t) = N$ の場合、$\frac{N}{DF(t)} = 1$ となり、対数を取ると $\log(1) = 0$ になります。これは、全ての文書で出現する単語が重要度を持たない（0 になる）ことを意味し、TF-IDF の意図と一致します。
- しかし、数学的な安定性のために $+1$ を加えることで、スコアが 0 になるケースを防ぎます。

## 4. まとめ

DF の対数を取る理由は以下の通りです：

1. 値をスケールダウンし、極端な値を抑える。
2. 自然言語の性質に適合した非線形な重要度スコアを与える。
3. 計算を安定させる。
4. すべての文書で出現する単語（$DF = N$）を適切に無視できるようにする。

TF-IDF の設計は、単語の希少性と頻度に基づいて、単語の重要性をバランスよく評価することを目的としていることがわかりますね。
