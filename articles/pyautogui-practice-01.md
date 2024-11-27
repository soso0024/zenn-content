---
title: "PyAutoGUIでYouTubeのプレイリスト自動再生プログラム作ってみた"
emoji: "📺"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["python", "pyautogui", "youtube"]
published: true
---

## TL;DR

今回は，PyAutoGUI で YouTube のプレイリスト自動再生プログラムを作成する方法を紹介します。
作成理由としては，勉強する際に自身で Youtube を検索すると，ホーム画面に表示されたおすすめの動画に気を取られてしまい，勉強に集中できないことがあったためです。

⚠️：動画のサイズ上，圧縮してデモ動画を掲載しているので，動きが遅いですが，実際の動作はもっと速いです。
![](/images/pyautogui-practice-01/study_bgm.gif)

## PyAutoGUI とは

- PyAutoGUI は，Python で GUI 操作を自動化するためのライブラリ
- マウスやキーボードの操作を自動化することができる

さらに詳しく知りたい方は，こちらの動画がオススメです
https://youtu.be/c85tbunlF2c?si=LNTaWYAR10wpJHt4

### インストール

```bash
pip install pyautogui
```

## 作成したプログラム

```python
import pyautogui  # マウスとキーボードを自動制御するためのPyAutoGUIライブラリをインポート
from time import sleep  # プログラムの実行を一時停止するためのsleep関数をインポート

# ブラウザでYouTubeを開くための一連のコマンド
# 画面上の座標(400, 50)をクリックして、ブラウザのアドレスバーにフォーカスを移動
pyautogui.click(400, 50)

# アドレスバーに「youtube.com」と入力
# PyAutoGUIのtypewrite関数を使用して、指定された文字列を自動入力
pyautogui.typewrite("youtube.com")

# Enterキーを押して、URLに移動
# typewrite関数の特殊キー入力機能を使用して、Enterキーの押下をシミュレート
pyautogui.typewrite(["enter"])

# YouTubeのページが完全に読み込まれるまで5秒間待機
# ページの読み込みが完了する前に次の操作を行うことを防ぐため
sleep(5)

# 画面上の座標(150, 390)をクリックして、プレイリストボタンを選択
# YouTubeの左側メニューにあるプレイリストセクションに移動
pyautogui.click(150, 390)

# プレイリストページが読み込まれるまで2秒間待機
# ページの遷移とコンテンツの読み込みを待機
sleep(2)

# 画面上の座標(450, 590)をクリックして、勉強用BGMのプレイリストを選択
# 注意：画面の解像度やブラウザのウィンドウサイズによって、
# 正確な座標は異なる可能性があるため、個々の環境に応じて調整が必要
pyautogui.click(450, 590)
```

上記の Brave ブラウザで，Mac Book Air の 13.6-inch (2560 × 1664)の画面に対応しています。
プログラムを実行する際には，自身の環墩に合わせて座標を調整してください。
