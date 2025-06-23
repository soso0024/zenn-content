---
title: "言語化能力身につけたい"
emoji: "😗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["AtCoder", "Python", "C問題"]
published: false
---

今回は，Atcoder Beginner Contest 378 C 問題を解いた際のメモを残しておきます．
AI を使う時に，自分の考えを言語化する能力って必要ですよね ~
今回この問題を解いていて，自分の頭の中で考えていることを言語化できず， AI にどのようにアドバイスをすればいいかわからず，使用することができませんでした．
これからの時代，言語化能力はますます重要になってくると感じるので，毎日の継続した努力を続けていきたいです．

### [AtCoder Beginner Contest 378 C](https://atcoder.jp/contests/abc378/tasks/abc378_c)

https://atcoder.jp/contests/abc378/tasks/abc378_c

## 回答

計算量を考慮できていないため，TLE になったコードを載せておきます．

```python
n = int(input())
a = list(map(int, input().split()))

ans = [-1] * n
for i in range(1, n):
    ans_candi = -1
    for j in range(i):
        if a[j] == a[i]:
            ans_candi = j + 1
    ans[i] = ans_candi
print(" ".join(map(str, ans)))
```

2 重の for 文を使っているため，計算量が O(N^2) になってしまいます．
これを O(N) に変更した際のコードを載せておきます．

```python
n = int(input())
a = list(map(int, input().split()))

ans = [-1] * n
last_seen = {}
for i in range(n):
    if a[i] in last_seen:
        ans[i] = last_seen[a[i]] + 1
    last_seen[a[i]] = i  # index starts from 0
print(" ".join(map(str, ans)))
```

解決策は，辞書型を使って，過去に出現した数字のインデックスを記録するようにしました．
数字のインデックスは 0 から始まるため，ans 配列にインデックスを記録する際に +1 しています．

::: details 例題における last_seen の変化

1. 初期状態:

   - last_seen は空の辞書です。

2. 1 回目のループ (i=0):

   - a[0]は 1 です。
   - 1 は last_seen に存在しないため、ans[0]は変更されません。
   - last_seen に 1 をキーとして追加し、その値を 0 に設定します。
   - last_seen の状態: {1: 0}

3. 2 回目のループ (i=1):

   - a[1]は 2 です。
   - 2 は last_seen に存在しないため、ans[1]は変更されません。
   - last_seen に 2 をキーとして追加し、その値を 1 に設定します。
   - last_seen の状態: {1: 0, 2: 1}

4. 3 回目のループ (i=2):

   - a[2]は 1 です。
   - 1 は last_seen に存在するため、ans[2]を last_seen[1] + 1 に設定します（0 + 1 = 1）。
   - last_seen の 1 の値を 2 に更新します。
   - last_seen の状態: {1: 2, 2: 1}

5. 4 回目のループ (i=3):

   - a[3]は 1 です。
   - 1 は last_seen に存在するため、ans[3]を last_seen[1] + 1 に設定します（2 + 1 = 3）。
   - last_seen の 1 の値を 3 に更新します。
   - last_seen の状態: {1: 3, 2: 1}

6. 5 回目のループ (i=4):

   - a[4]は 3 です。
   - 3 は last_seen に存在しないため、ans[4]は変更されません。
   - last_seen に 3 をキーとして追加し、その値を 4 に設定します。
   - last_seen の状態: {1: 3, 2: 1, 3: 4}

結果
最終的に、last_seen は次のような状態になります：

```plaintext
{1: 3, 2: 1, 3: 4}
```

これは、リスト a の各要素が最後に出現したインデックスを示しています。例えば、1 はインデックス 3 で最後に出現し、2 はインデックス 1 で最後に出現し、3 はインデックス 4 で最後に出現しています。
