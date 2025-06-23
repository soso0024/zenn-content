---
title: "Prepare for Huckson Internship"
emoji: "🐍"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [internship, huckson, typescript]
published: false
---

# TypeScript

[こちら](https://typescriptbook.jp/)の記事を参考に，TypeScript の基本的な使い方を学びます．

## 静的型付け

- TypeScript は，静的型付け言語
  - C 言語，Java も同じ
  - コンパイル時に変数の型が定まる言語
    - 型にまつわる問題はプログラムを実行しなくても発見できる
- 動的型付け言語
  - Python，Ruby，JavaScript
  - 実行時に変数の型が定まる言語 - 型にまつわる問題はプログラムを実行しないと発見できない
    詳しくはこちら：[静的型付け](https://typescriptbook.jp/overview/static-type)

## アロー関数

https://typescriptbook.jp/reference/functions/arrow-functions

## ジェネリクス

- ジェネリクスは，型も引数のように扱うという発想
- コードの共通化と型の安全性を両立するための言語機能

```typescript
function chooseRandomly<String>(v1: <string>, v2: <string>): <string> {
  return Math.random() <= 0.5 ? v1 : v2;
}
function chooseRandomly<Number>(v1: <number>, v2: <number>): <number> {
  return Math.random() <= 0.5 ? v1 : v2;
}
function chooseRandomly<URL>(v1: <URL>, v2: <URL>): <URL> {
  return Math.random() <= 0.5 ? v1 : v2;
}
chooseRandomly<String>("勝ち", "負け");
chooseRandomly<Number>(1, 2);
chooseRandomly<URL>(urlA, urlB);
```

上記をジェネリクスを使って書き換えると以下のようになる．

```typescript
// 注意: これは架空の文法です
function chooseRandomly<type>(v1: <type>, v2: <type>): <type> {
  return Math.random() <= 0.5 ? v1 : v2;
}
chooseRandomly<string>("勝ち", "負け");
chooseRandomly<number>(1, 2);
chooseRandomly<URL>(urlA, urlB);
```

https://typescriptbook.jp/reference/generics

# React

## DOM

- DOM(Document Object Model)は，画面を表示するまでに解釈した HTML/CSS/JavaScript によって構築された DOM（ツリー）を指す
  - https://qiita.com/seira/items/6767e222890c9890ecb9
- It is a structural representation of HTML elements of a web application in simple words.

## props と state

- props: 親コンポーネント子コンポーネントへ値を渡すための仕組み
- state: 各コンポーネントごとに持つ，コンポーネントの状態を管理する仕組み
  ![](/images/intern-huckson-mynav/proprs_state.jpg)
  引用：https://qiita.com/rio_threehouse/items/7632f5a593cf218b9504
