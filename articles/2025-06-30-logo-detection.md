---
title: "ロゴ検出"
emoji: "🧌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["python", "opencv", "selective_search", "pytorch"]
published: false
---

## 目的

大学院の授業でロゴ検出システム構築の課題が出たので，それを解いた際の自分用のメモとして記述しています．

## システム要件

1. Selective Search を用いて，画像中の物体を検出する
2. Pre-trained CNN を用いて，ステップ 1 で検出した物体の特徴ベルトルを抽出する
3. SVM を用いて，Binary Classification (2 値分類)を行い，ロゴかどうかを判定する

### 制約

- Deep Learning には pytorch を使用する
- Pre-trained CNN には Vision Transformer を $\textcolor{red}{使用しない}$
-
