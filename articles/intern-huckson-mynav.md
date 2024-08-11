---
title: "Prepare for Huckson Internship"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [internship, huckson, typescript]
published: true
---
# TypeScript
[こちら](https://typescriptbook.jp/)の記事を参考に，TypeScriptの基本的な使い方を学びます．
## 静的型付け
- TypeScriptは，静的型付け言語
  - C言語，Javaも同じ
  - コンパイル時に変数の型が定まる言語
    - 型にまつわる問題はプログラムを実行しなくても発見できる
- 動的型付け言語
  - Python，Ruby，JavaScript
  - 実行時に変数の型が定まる言語
    - 型にまつわる問題はプログラムを実行しないと発見できない
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