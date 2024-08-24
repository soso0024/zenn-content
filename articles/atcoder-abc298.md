---
title: "AtCoder - Coloring Matrix"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [atcoder, python]
published: true
---

今回は，AtCoder Beginner Contest 311 B 問題を解いた際につまずいたポイントに関するメモを残しておきます．

[Coloring Matrix](https://atcoder.jp/contests/abc311/tasks/abc311_b)

https://atcoder.jp/contests/abc311/tasks/abc311_b

## 自分の回答

```python
N = int(input())
A, B = [], []
for _ in range(N):
    A.append(list(map(int, input().split())))
for _ in range(N):
    B.append(list(map(int, input().split())))

ans = "No"
for cnt in range(4):
    flag = True
    for i in range(N):
        for j in range(N):
            if A[i][j] == 1:
                if B[i][j] != 1:
                    flag = False
    if flag:
        ans = "Yes"
        break

    tmp = A
    A = [[0] * N for _ in range(N)]
    for ni in range(N):
        for nj in range(N):
            A[ni][nj] = tmp[N - 1 - nj][ni]

print(ans)
```

## つまずいたところ

- 参照渡しの挙動がわからなかった．
  - tmp = A とした際に，tmp の値を変更すると A の値も変更されてしまうことに気づかなかった．
  - A 初期化`A = [[0] * N for _ in range(N)]` することで解決．
