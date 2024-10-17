---
title: "Pythonの深淵に触れる"
emoji: "🐍"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Python", "GeekCamp"]
published: true
---

技育キャンプ で 「エムスリー流！難読クイズを作って Python の深淵に触れるコツ！」 の講義を受けた際のアウトプット．

## 問題 1

```python
000_0&00^00-0x0_0_00^0o0_00-~0^-0b0_0_0
```

:::details 回答

```python
1
```

:::

:::details 解説
押さえておくべき Python 構文
![](/images/geek-camp-python/q1/integer_literal.png)
![](/images/geek-camp-python/q1/and_not_xor.png)

上記の構文を利用して，問題を分解すると以下のようになる．

```python
0 & 0 ^ 0 - 0 ^ 0 - ~0 ^ -0
👇
(0 & 0 ^ 0) - (0 ^ 0) - (~0 ^ -0)
👇
0 - 0 - (-1)
👇
1
```

:::

## 問題 2

```python
...is...is...is...is...is...
```

:::details 回答

```python
True
```

:::

:::details 解説
連続した比較演算子 is は同時に評価される．
:::

## 問題 3

```python
list(map(list,list(map(map,map(lambda map:list,map:='map'),map))))
```

:::details 回答

```python
[[['m']], [['a']], [['p']]]
```

:::

:::details 解説
押さえておくべき Python 構文
![](/images/geek-camp-python/q3/map.png)
![](/images/geek-camp-python/q3/sep01.png)
![](/images/geek-camp-python/q3/sep02.png)
![](/images/geek-camp-python/q3/sep03.png)
![](/images/geek-camp-python/q3/sep04.png)

:::

## 問題 4

```python
*t,M=[*"Hello World! 3"];"M"+M
```

:::details 回答

```python
M3
```

:::

:::details 解説
分けて考えると以下のようになる．

```python
*t, M = [*"Hello World! 3"]
"M" + M
```

`M` には `3` が代入され，
`t` には `['H', 'e', 'l', 'l', 'o', ' ', 'W', 'o', 'r', 'l', 'd', '!', ' ']` が代入される．
結果，`"M" + M` は `"M3"` となる．

:::details アンパック演算子 ChatGPT
この Python のコードについて説明させていただきます。

`*M,M = [*"123"]` という式を分解して説明しましょう：

1. まず `"123"` は文字列です

2. `[*"123"]` の部分:

   - `*` はアンパック演算子で、イテラブル（文字列やリストなど）を展開します
   - 文字列 "123" が展開されて、リスト `['1', '2', '3']` になります

3. `*M,M = ` の部分:
   - 左側の `*M` はスター付きの変数で、残りの要素をすべて受け取ります
   - 最後の `M` は 1 つの要素を受け取ります

したがって、この式の実行結果は：

- `M = '3'` （最後の要素）
- 最初の `M` は `['1', '2']` （残りの要素のリスト）

実際に試してみましょう：

```python
*M,M = [*"123"]
print(f"最初のM: {M}")  # 最初のM: 3
print(f"スター付きのM: {M}")  # スター付きのM: ['1', '2']
```

これは、Python のアンパッキング（展開）とシーケンスの分割代入を組み合わせた少し複雑な例です。主にリストや文字列の要素を分割して異なる変数に代入する際に使用されます。
:::
