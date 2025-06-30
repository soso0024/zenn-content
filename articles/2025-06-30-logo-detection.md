---
title: "ロゴ検出"
emoji: "🧌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["python", "opencv", "pytorch"]
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

## 開発メモ

### ステップ 1: Selective Search を用いて，画像中の物体を検出する

- Selective Search: 画像内の似ているピクセルをまとめるための教示なしアルゴリズム
  - 参考：https://blog.shikoan.com/selective-search-rcnn/
- Region Proposal: 1 枚の画像の中から切り出した画像（候補）のこと

### ステップ 2: Pre-trained CNN を用いて，ステップ 1 で検出した物体の特徴ベルトルを抽出する

- Pre-trained CNN: ResNet
  - ResNet: 画像認識モデル
  - 参考: https://dx-consultant-fast-evolving.com/resnet/

### ステップ 3: SVM を用いて，Binary Classification (2 値分類)を行い，ロゴかどうかを判定する

- SVM: 教示あり学習における，分類のタスクで主に使用される．特に，2 つのクラスを分離する最適な超平面（2 次の場合は直線）を見つけることに重点を置いている．
  - 参考: https://qiita.com/c60evaporator/items/8864f7c1384a3c6e9bd9
