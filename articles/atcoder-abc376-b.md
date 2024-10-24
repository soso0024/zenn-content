---
title: "これ本当にB問題？"
emoji: "🤔"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["AtCoder", "Python", "B問題"]
published: true
---

今回は，AtCoder Beginner Contest 376 B 問題を解いた際のメモを残しておきます．
最近の AtCoder は，B 問題でもちょっとした工夫が必要なことが多いですね．
「AI の進化により，難易度が上がっているのかも」とか思ってしまいます．

### [AtCoder Beginner Contest 376 B](https://atcoder.jp/contests/abc376/tasks/abc376_b)

https://atcoder.jp/contests/abc376/tasks/abc376_b

## 回答

自分で記述したコードはすごい冗長でしたので，解説のコードを載せておきます．

```python
def num_move(n, from_, to, ng):
    if from_ > to:
        from_, to = to, from_  # always from_ <= to

    if from_ <= ng <= to:
        return from_ + n - to  # (from_ - 1) + 1 + (N - to)
    else:
        return to - from_


n, q = map(int, input().split())
l, r = 1, 2
ans = 0
for _ in range(q):
    h, t = input().split()
    t = int(t)
    if h == "L":
        ans += num_move(n, l, t, r)
        l = t
    else:
        ans += num_move(n, r, t, l)
        r = t
print(ans)
```

::: details N + from - to が理解できない場合
計算式 **\( (from - 1) + 1 + (N - to) = N + from - to \)** は、円環上で「ng」を避けて「from」から「to」まで最短で移動する回数を表しています。

### 前提と設定

- **\( N \)** は円環の全体の大きさ、つまり最も大きな番号です。例えば、円環が 1 から N までの場所を持っていると考えます。
- **\( from \)** はスタート位置、**\( to \)** はゴール位置です。
- ただし、途中に **\( ng \)** という避けなければならない位置があります。

### 逆方向に移動する場合

「ng」が \( from → to \) の間にある場合、順方向ではなく逆方向（つまり反時計回りに）に回る必要があります。逆方向に進む場合の移動回数は、次のようにして導かれます。

1. **\( from \) から 1 まで移動する回数**  
   \( from → 1 \) までの移動回数は \( from - 1 \) です。

2. **円環の最後の位置 \( N \) に移動する**  
   円環上では 1 の次が\( N \)であるため、1 から \( N \) への移動回数は 1 回です。

3. **\( N \) から \( to \) まで移動する回数**  
   最後に \( N → to \) に移動します。これには \( N - to \) 回かかります。

これら 3 つのステップを足し合わせると、次のような式が導かれます：

```plaintext
(from - 1) + 1 + (N - to)
```

これは、スタートから反対方向に回ってゴールにたどり着くまでの全体の移動回数を表しています。

### 式の簡略化

上記の式を整理すると、次のように簡略化できます：

```plaintext
(from - 1) + 1 + (N - to) = N + from - to
```

つまり、最終的な式は \( N + from - to \) というシンプルな形にまとめることができます。

### まとめ

この式は、「ng」を避けるために逆方向（反時計回り）に移動する場合の移動回数を計算するもので、以下の 3 つの要素に分解されます：

- \( from \) から \( 1 \) までの移動回数
- 1 から \( N \) への移動（円環のつながりを考慮）
- \( N \) から \( to \) までの移動

最終的にこの合計が \( N + from - to \) という形になります。
:::

６通りに場合分けして考える場合は，下記の記事が参考になります．

https://yuulis.hatenablog.com/entry/ABC-376-B
