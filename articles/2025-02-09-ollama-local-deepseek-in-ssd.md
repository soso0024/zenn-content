---
title: "DeepSeek R1モデルをSSDに保存して使う方法"
emoji: "🐳"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["ollama", "deepseek", "deepseek r1", "cline", "ssd"]
published: true
---

Ollama を使用して DeepSeek R1 モデルをローカル環境で使用する際に，パソコンのストレージ容量が不足していて，モデルを SSD に保存して使用する必要があったので，その方法を備忘録としてまとめました．

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

すでに，`ollama pull` でモデルをダウンロードしている場合は，`~/.ollama/models` にモデルが保存されている．
下記のコマンドを実行して，`blobs` と `manifests` フォルダを SSD に移動する．

```bash
mv ~/.ollama/models/blobs /Volumes/MySSD/Ollama/
mv ~/.ollama/models/manifests /Volumes/MySSD/Ollama/
```

:::details Ollama のインストールがまだの場合
Ollama のインストールは下記から！

- 公式サイト
  https://ollama.com/download

- brew でインストールする場合
  https://formulae.brew.sh/formula/ollama
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

## 最後に

今回私がローカル環境で DeepSeek R1 モデルを使用するきっかけとなったのは [Cline](https://cline.bot/) で使用するためです．
興味がある人は下記の記事を参考にやってみてください！
https://apidog.com/blog/free-deepseek-r1-vscode-cline/
