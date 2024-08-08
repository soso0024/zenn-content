---
title: "Who is Saikyo?"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [atcoder, python]
published: true
---
今回は，AtCoder Beginner Contest 313 B問題を解いた際の考え方に関するメモを残しておきます．

# [Who is Saikyo?](https://atcoder.jp/contests/abc313/tasks/abc313_b)

## 回答例
```python
N, M = map(int, input().split())
info_strength = []
for _ in range(M):
    info_strength.append(list(map(int, input().split())))

s = [0] * N
# print(s)
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
0で初期化した配列`s`を用意し，その配列を「自分より強い人の人数」を管理する配列として利用します．`info_strength`の要素の2番目は「自分より強い人がいることを示す番号」なので，その番号-1をインデックスとして，配列`s`の要素をインクリメントします（自分より強い人がいることを示す）．sの要素が0の場合，その人が最強であることを示すので，その番号を出力します．0の要素が2つ以上ある場合は，最強の人が複数いることを示すので，-1を出力します．
