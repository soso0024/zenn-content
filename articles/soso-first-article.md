---
title: "First Article"
emoji: "🐙"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["memo"]
published: true
---

# 初めての記事
私は zenn を使用して，自分の読書や学習のメモを書いていきます．
継続して書くことで，自分の学習のアウトプットを増やすことを目標にしています．

# きっかけ
世界一流エンジニア思考法を読んで，自分の学習のアウトプットを増やすことの重要性を感じたことがきっかけです．また，自身の備忘録としても活用できると思い，zennを使用することにしました．
こちらの本を読んで，天才でも一回の学習で完璧に理解することは難しいということがわかりました．
自分は，一回の学習で理解できないと諦めてしまう癖があるので，その点を改善したいと思いました．
また，「理解する」ということに時間をかけることで，自分の知識が深まるということも学びました．

# zennをgithubと連携する際につまづいた点
brewでnode.jsをインストール後，node -vでバージョンを確認すると，バージョンが最新のものになっていなかった．
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
確認のために，node -vでバージョンを確認した．
```
node -v
```

# 参考記事
- [GitHubリポジトリでZennのコンテンツを管理する](https://zenn.dev/zenn/articles/connect-to-github)
- [Zenn CLIをインストールする](https://zenn.dev/zenn/articles/install-zenn-cli)
- [Zenn CLIで記事・本を管理する方法](https://zenn.dev/zenn/articles/zenn-cli-guide)
- [Zenn Docs Github](https://github.com/zenn-dev/zenn-docs)
- [Zenn Markdown記法](https://zenn.dev/zenn/articles/markdown-guide)