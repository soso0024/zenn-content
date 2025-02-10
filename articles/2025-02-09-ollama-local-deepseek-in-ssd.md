---
title: "DeepSeek R1モデルをSSDに保存して使う方法"
emoji: "🐳"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Ollama", "DeepSeek", "DeepSeek-R1", "Cline", "SSD"]
published: true
---

Ollama を使用して [DeepSeek R1 モデル](https://ollama.com/library/deepseek-r1)をローカル環境で使用する際に，パソコンのストレージ容量が不足していて，モデルを SSD に保存して使用する必要があったので，その方法を備忘録としてまとめました．

## TD;LR

1. SSD にモデルを保存するためのフォルダを作成
2. モデルの情報を SSD に保存
3. リンクを作成する

## もう少し詳しく

### 1. SSD にモデルを保存するためのフォルダを作成

- SSD に接続
- モデルの保存先のフォルダを作成
  - 今回は，`Ollama` というフォルダを保存先として作成
  - `MySSD`の部分は自身の SSD の名前に置き換え

```bash
mkdir /Volumes/MySSD/Ollama
```

### 2. モデルの情報を SSD に保存

すでに，`ollama pull` でモデルをダウンロードしている場合は，`~/.ollama/models` にモデルが保存されている．（macOS の場合）
Linux, Windows を使用している場合は，下記のリンクを参考にしてください．
https://github.com/ollama/ollama/blob/main/docs/faq.md#where-are-models-stored
下記のコマンドを実行して，`blobs` と `manifests` フォルダを SSD に移動する．

```bash
mv ~/.ollama/models/blobs /Volumes/MySSD/Ollama/
mv ~/.ollama/models/manifests /Volumes/MySSD/Ollama/
```

:::details Ollama のインストール，DeepSeek R1 モデルのダウンロード
Ollama のインストールは下記から！

- 公式サイト
  https://ollama.com/download

- brew でインストールする場合
  https://formulae.brew.sh/formula/ollama

Ollama を使用した DeepSeek R1 モデルのダウンロードは下記から！

- https://dev.to/extinctsion/how-run-deepseek-locally-with-ollama-4kbd
  :::

:::details DeepSeek R1 モデルサイズと推奨されるハードウェアのスペック
| MODEL | RAM NEEDED | RECOMMENDED GPU |
| ----- | ---------- | --------------- |
| 1.5B | 4GB | Integrated |
| 7B | 8–10GB | NVIDIA GTX 1660 |
| 14B | 16GB+ | RTX 3060/3080 |
| 70B | 40GB+ | RTX 4090/A100 |
:::

### 3. リンクを作成する

`~/.ollama/models` にリンクを作成する．
Ollama にモデルがどこに保存されているかを”教える”ために，リンクを作成する．
今回は`blobs` と `manifests` フォルダを SSD に保存したので，それぞれのリンクを作成する．

```bash
ln -s "/Volumes/MySSD/Ollama/blobs" ~/.ollama/models/blobs
ln -s "/Volumes/MySSD/Ollama/manifests" ~/.ollama/models/manifests
```

リンクが正しく作成されているかを確認する．

```bash
ls -l ~/.ollama/models
```

### 動作確認

`ollama run deepseek-r1:7b`を実行して動作していることが確認できたら，終了です！

:::message
`deepseek-r1:7b` は DeepSeek R1 モデルのバージョンを指定しています．
自分の使用しているバージョンに合わせて変更してください！
:::

## 最後に

今回私がローカル環境で DeepSeek R1 モデルを使用するきっかけとなったのは [Cline](https://cline.bot/) で使用するためです．
興味がある人は下記の記事を参考にやってみてください！
https://apidog.com/blog/free-deepseek-r1-vscode-cline/
