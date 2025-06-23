---
title: "AtCoder - Who is Saikyo?"
emoji: "🐍"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [atcoder, python]
published: false
---

今回は，AtCoder Beginner Contest 313 B 問題を解いた際の考え方に関するメモを残しておきます．

### [Who is Saikyo?](https://atcoder.jp/contests/abc313/tasks/abc313_b)

https://atcoder.jp/contests/abc313/tasks/abc313_b

## 自分の回答

```python
N, M = map(int, input().split())
info_strength = []
for _ in range(M):
    info_strength.append(list(map(int, input().split())))

s = [0] * N
for i in range(M):
    s[info_strength[i][1] - 1] += 1

cnt = 0
No1 = -1
for i in range(len(s)):
    if s[i] == 0:
        cnt += 1
        No1 = i + 1
if cnt == 1:
    print(No1)
else:
    print(-1)
```

## 考え方

0 で初期化した配列`s`を用意し，その配列を「自分より強い人の人数」を管理する配列として利用します．`info_strength`の要素の 2 番目は「自分より強い人がいることを示す番号」なので，その番号-1 をインデックスとして，配列`s`の要素をインクリメントします（自分より強い人がいることを示す）．s の要素が 0 の場合，その人が最強であることを示すので，その番号を出力します．0 の要素が 2 つ以上ある場合は，最強の人が複数いることを示すので，-1 を出力します．
