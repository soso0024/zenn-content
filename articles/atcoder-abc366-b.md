---
title: "AtCoder - Vertival Writing"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [atcoder, python]
published: false
---

今回は，AtCoder Beginner Contest 366 B 問題を解いた際につまずいたポイントに関するメモを残しておきます．

### [Vertival Writing](https://atcoder.jp/contests/abc366/tasks/abc366_b)

https://atcoder.jp/contests/abc366/tasks/abc366_b

## 自分の回答

```python
N = int(input())
S = []
max_length = -1
for _ in range(N):
    s = input()
    S.append(s)
    max_length = max(max_length, len(s))

S = [s.ljust(max_length) for s in S]
print()

for j in range(max_length):
    output = []
    last_character = []
    for i in range(N - 1, -1, -1):
        if S[i][j] == " " and i == 0:
            break

        if S[i][j] == " ":
            output.append("*")
            last_character = "*"
        else:
            output.append(S[i][j])
            last_character = S[i][j]

    while output[-1] == "*":  # つまずきポイント
        output.pop()
    print("".join(output))
```

## つまずいたところ

- 配列の大きさを全て統一し，不足している部分を空白で埋める処理の実装方法がわからなかった．
  - `ljust`メソッドを使って，文字列の右側に空白を追加することで解決しました．
- 入力として下記のようなものが与えられる場合に，最後の＊を適切に削除することができていなかった．（＊が二つ以上末尾に並んだ場合に，削除することができていなかった）
  - Input：
    4
    abc
    abc
    as
    dddi
  - `while output[-1] == "*":`のように，最後の要素が空白である限り，要素を削除する処理を追加することで解決しました．
