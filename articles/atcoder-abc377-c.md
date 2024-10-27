---
title: "久しぶりのC問題，なんとか解けた"
emoji: "🫠"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["atcoder", "python", "C問題"]
published: true
---

今回は，AtCoder Beginner Contest 377 C 問題を解いた際のメモを残しておきます．
C 問題は久しぶりに解いたのですが，なんとか自力で解けました．

### [AtCoder Beginner Contest 377 C](https://atcoder.jp/contests/abc377/tasks/abc377_c)

https://atcoder.jp/contests/abc377/tasks/abc377_c

## 回答

今回は，与えられた条件をそのまま実装することで解くことができました．

```python
n, m = map(int, input().split())
coordinates = set(tuple(map(int, input().split())) for _ in range(m))

coord_list = list(coordinates)

a = set()
for i in range(m):
    a.add((coord_list[i][0] - 1, coord_list[i][1] - 1))

    if coord_list[i][0] - 1 + 2 < n and coord_list[i][1] - 1 + 1 < n:
        # 座標をタプルとしてsetに追加
        a.add((coord_list[i][0] - 1 + 2, coord_list[i][1] - 1 + 1))

    if coord_list[i][0] - 1 + 1 < n and coord_list[i][1] - 1 + 2 < n:
        a.add((coord_list[i][0] - 1 + 1, coord_list[i][1] - 1 + 2))

    if coord_list[i][0] - 1 - 1 > -1 and coord_list[i][1] - 1 + 2 < n:
        a.add((coord_list[i][0] - 1 - 1, coord_list[i][1] - 1 + 2))

    if coord_list[i][0] - 1 - 2 > -1 and coord_list[i][1] - 1 + 1 < n:
        a.add((coord_list[i][0] - 1 - 2, coord_list[i][1] - 1 + 1))

    if coord_list[i][0] - 1 - 2 > -1 and coord_list[i][1] - 1 - 1 > -1:
        a.add((coord_list[i][0] - 1 - 2, coord_list[i][1] - 1 - 1))

    if coord_list[i][0] - 1 - 1 > -1 and coord_list[i][1] - 1 - 2 > -1:
        a.add((coord_list[i][0] - 1 - 1, coord_list[i][1] - 1 - 2))

    if coord_list[i][0] - 1 + 1 < n and coord_list[i][1] - 1 - 2 > -1:
        a.add((coord_list[i][0] - 1 + 1, coord_list[i][1] - 1 - 2))

    if coord_list[i][0] - 1 + 2 < n and coord_list[i][1] - 1 - 1 > -1:
        a.add((coord_list[i][0] - 1 + 2, coord_list[i][1] - 1 - 1))

print(n * n - len(a))
```

## つまずいたところ

- タイポ
- set の特徴
  - set では添え字（インデックス）による直接アクセスができない
  - 順序が保証されない
  - アクセスする場合は，リストに変換してからアクセスする必要がある
- ユーザからの入力はインデックスが 1 から始まるが，プログラム内では 0 から始まる
  - マイナス１してから，処理する必要がある
-

## 公式の回答

```python
n,m = map(int,input().split())
s = set()
for _ in range(m):
  a,b = map(int,input().split())
  s.add((a,b))
  for i in [-1,1]:
    for j in [-2,2]:
      s.add((a+i,b+j))
      s.add((a+j,b+i))
ans = n*n
for i,j in s:
  if 1<=i<=n and 1<=j<=n:
    ans-=1
print(ans)
```
