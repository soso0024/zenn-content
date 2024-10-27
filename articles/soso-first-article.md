---
title: "First Article"
emoji: "🥇"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["memo"]
published: true
---

## 初めての記事

私は zenn を使用して，自分の読書や学習のメモを書いていきます．
継続して書くことで，自分の学習のアウトプットを増やすことを目標にしています．

## きっかけ

「世界一流エンジニア思考法」という本を読んで、以下の重要な気づきを得ました：

1. 学習した内容をアウトプットすることの大切さ

   - これをきっかけに、Zenn でブログを始めることを決意
   - 自分の学習記録としても活用できると考えました

2. 学習における新しい発見

   - 天才エンジニアでさえ、一度の学習で完璧に理解することは難しい
   - これまでの自分は、一度で理解できないと諦めてしまう傾向があった
   - この考え方を改める必要性を感じました

3. 深い理解の重要性
   - 「理解する」というプロセスに時間をかけることで，より深い知識を得られることを学びました

この本から、学習は一回で完璧を目指すのではなく、継続的な理解の深化が重要だということを学ぶことができました．

## zenn を github と連携する際につまづいた点

brew で node.js をインストール後，node -v でバージョンを確認すると，バージョンが最新のものになっていなかった．
そのため，下記のコマンドを実行して，`node@20`の実行ファイルのパスを通した．

```
echo 'export PATH="/opt/homebrew/opt/node@20/bin:$PATH"' >> ~/.zshrc
echo 'export LDFLAGS="-L/opt/homebrew/opt/node@20/lib"' >> ~/.zshrc
echo 'export CPPFLAGS="-I/opt/homebrew/opt/node@20/include"' >> ~/.zshrc
```

設定を反映させるために，下記のコマンドを実行した．

```
source ~/.zshrc
```

確認のために，node -v でバージョンを確認した．

```
node -v
```

## 参考記事

- [GitHub リポジトリで Zenn のコンテンツを管理する](https://zenn.dev/zenn/articles/connect-to-github)
- [Zenn CLI をインストールする](https://zenn.dev/zenn/articles/install-zenn-cli)
- [Zenn CLI で記事・本を管理する方法](https://zenn.dev/zenn/articles/zenn-cli-guide)
- [Zenn Docs Github](https://github.com/zenn-dev/zenn-docs)
- [Zenn Markdown 記法](https://zenn.dev/zenn/articles/markdown-guide)
