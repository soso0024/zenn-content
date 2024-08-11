---
title: "Vacation Together"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [atcoder, python]
published: true
---
今回は，AtCoder Beginner Contest 311 B 問題を解いた際につまずいたポイントに関するメモを残しておきます．

### [Vacation Together](https://atcoder.jp/contests/abc311/tasks/abc311_b)
https://atcoder.jp/contests/abc311/tasks/abc311_b

## 自分の回答
```python
n, d = map(int, input().split())
s = []
for _ in range(n):
    s.append(input())
tmp = []
for j in range(d):
    ok = True
    for i in range(n):
        if s[i][j] == "x":
            ok = False
    if ok:
        tmp += "o"
    else:
        tmp += "x"
a = 0
ans = 0
for i in range(d):
    if tmp[i] == "o":
        a += 1
    else:
        a = 0
    ans = max(ans, a)
print(ans)

```

## つまずいたところ
- 配列の要素を縦に見る方法がわからなかった．
