---
title: "ClaudeのMCPとProjectを使用して研究プロセスを自動化してみた"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["claude", "mcp", "research", "project"]
publication_name: nislab
published: true
---

## TL;DR

Claude MCP サーバーと Claude Project を使用し，研究プロセス(論文検索 → 要約 → 保存)を自動化する方法を紹介します。出力結果は，落合陽一フォーマットで出力し，保存されるようにしました。

:::details 落合陽一フォーマットとは

1. どんな研究？
2. 先行研究と比べて何がすごい？
3. 手法のきもはどこ？
4. 手法の有効性をどう検証した？
5. 議論はあるか？
6. 次に読むべき論文は？

論文の要点を上記の 6 つの観点からまとめるフォーマット。
:::

## 私たちの研究室(NISLab)

https://nisk.doshisha.ac.jp/

## アドベントカレンダー 5 日目~

https://nislab-advent-calendar-2024-12.vercel.app/

## 使用する MPC サーバー

- [Brave Search](https://github.com/modelcontextprotocol/servers/tree/main/src/brave-search)
- [Filesystem](https://github.com/modelcontextprotocol/servers/tree/main/src/filesystem)

## Mac で MCP を使用するための準備

下記の記事の手順で進めてください。

https://zenn.dev/nislab/articles/2024-11-30-claude-mcp#mac%E3%81%A7mcp%E3%82%92%E4%BD%BF%E7%94%A8%E3%81%99%E3%82%8B%E3%81%9F%E3%82%81%E3%81%AE%E6%BA%96%E5%82%99

:::details Could not connect to MCP server エラーが発生した場合

エラーが出力された場合，Node のバージョンに問題がある可能性があります。[brave-search/pkage.json](https://github.com/modelcontextprotocol/servers/blob/main/src/brave-search/package.json)を見ると，`"@types/node": "^20.10.0"`と記載されており，[filesystem/package.json](https://github.com/modelcontextprotocol/servers/blob/main/src/filesystem/package.json)を見ると，`"@types/node": "^20.11.0"`と記載されています。
そのため，今回は Node のバージョンを 20.11.0 以上 21.0.0 未満に設定する必要があります。
私の場合は，nvm を使用して Node のバージョンを変更しました。

```bash
nvm install v20.18.1
nvm alias default v20.18.1
```

<br>
nvm のインストール方法は下記の記事を参考にしてください。
https://zenn.dev/nok_c7/articles/536ac2d35bf9e6

<br>

次に，使用する MPC サーバーをインストールします

```bash
npm install -g @modelcontextprotocol/server-brave-search
npm install -g @modelcontextprotocol/server-filesystem
```

<br>

最後に，`claude_desktop_config.json`を下記のように編集します
npx を削除し、グローバルにパッケージをインストールします。 また，Node 実行ファイルとサーバースクリプトの両方に絶対パスを使用します。

```json
{
  "mcpServers": {
    "brave-search": {
      "command": "/Users/<YourUsername>/.nvm/versions/node/<YourNodeVersion>/bin/node",
      "args": [
        "/Users/<YourUsername>/.nvm/versions/node/<YourNodeVersion>/lib/node_modules/@modelcontextprotocol/server-brave-search/dist/index.js"
      ],
      "env": {
        "BRAVE_API_KEY": "YOUR_BRAVE_API_KEY"
      }
    },
    "filesystem": {
      "command": "/Users/<YourUsername>/.nvm/versions/node/<YourNodeVersion>/bin/node",
      "args": [
        "/Users/<YourUsername>/.nvm/versions/node/<YourNodeVersion>/lib/node_modules/@modelcontextprotocol/server-filesystem/dist/index.js",
        "/Users/<YourUsername>/Desktop"
      ]
    }
  }
}
```

:::

## Claude Project とは

プロジェクトは、Claude Pro または Claude for Work プランのユーザーが、独自のチャット履歴とナレッジベースを持つ自己完結型のワークスペースを作成できるようにします。各プロジェクト内で、ドキュメントをアップロードし、コンテキストを提供し、Claude と焦点を絞ったチャットを行うことができます。

## Claude Project の設定

Project のタイトルを設定し，プロジェクトを作成します。
![](/images/2024-12-05-claude-mcp-research/claude_project_01.png)

`Add text content` をクリックし，テキストコンテンツを追加します。
![](/images/2024-12-05-claude-mcp-research/claude_project_02.png)

タイトルと内容を入力し，`Add Content` をクリックします。
![](/images/2024-12-05-claude-mcp-research/claude_project_03.png)

:::details コンテンツの内容

````markdown
# Research Analysis System Instructions

## システム概要

このシステムは、入力された研究テーマに関する学術論文を分析し、その研究内容を体系的に要約・整理するためのものです。システムは常に研究内容自体の分析に焦点を当て、入力された研究テーマに直接関連する学術的な知見のみを扱います。
Brave Search によって，ユーザからの入力に関する情報を必ず調べなさい。
調べた結果は下記のファイル・ディレクトリ構成で必ず保存するようにしなさい。

## ファイル管理

1. ディレクトリ作成

```bash
/Users/soso/Desktop/Internet Research/
```

2. ファイル命名規則

```
[SUBJECT]_research_[YYYY_MM_DD].md
```

### 重要な制約事項：

- 入力は必ず具体的な研究テーマまたは論文タイトルであること
- システムは研究内容の分析のみを行い、プロセスやワークフローの説明は行わない
- 出力は常に研究内容に関する具体的な分析結果のみとする

## 入力要件

- Subject Name（研究テーマ/論文タイトル）
- 発行日（1 年以内の研究を優先）

## 情報収集ソース

1. Google Scholar
2. ResearchGate
3. arXiv
4. ACM Digital Library
5. IEEE Xplore
6. Twitter Academic Research
7. Research-focused LinkedIn posts

## 研究分析基準

1. **情報収集の焦点**

   - 研究テーマに直接関連する学術論文のみを収集
   - 1 年以内に発表された査読付き論文を優先
   - 学術的な議論や引用のみを考慮
   - 技術ブログや SNS の一般的な議論は除外し、学術的な情報のみを使用

2. **分析フェーズ**

   - 収集した情報を 6 つの観点から整理:
     1. 研究概要の把握
     2. 先行研究との比較分析
     3. 主要な手法の特定
     4. 検証方法の評価
     5. 議論点の整理
     6. 関連研究の特定

3. **出力フェーズ**
   - マークダウン形式での文書化
   - ディレクトリ作成と保存
   - パス情報の表示

## 出力フォーマット

```markdown
# [Subject Name] Research Analysis

Date: YYYY-MM-DD

## 1. どんな研究？

[研究の主要な目的と成果]

## 2. 先行研究と比べて何がすごい？

[革新的なポイントと優位性]

## 3. 手法のきもはどこ？

[コア技術・手法の詳細]

## 4. 手法の有効性をどう検証した？

[実験設計と結果の概要]

## 5. 議論はあるか？

[限界点や将来の課題]

## 6. 次に読むべき論文は？

[関連する重要な研究リスト]

## 参考ソース

[最低 3 つの信頼できるソース]
```

## ファイル管理

1. ディレクトリ作成

```bash
/Users/soso/Desktop/Internet Research/
```

2. ファイル命名規則

```
[SUBJECT]_research_[YYYY_MM_DD].md
```

## 品質基準

- 学術性：査読付き論文や正式な研究発表のみを対象
- 最新性：1 年以内の研究を優先
- 信頼性：最低 3 つの学術的ソース（論文誌、学会 proceedings 等）
- 完全性：6 つの分析観点すべてを研究内容に即して分析
- 具体性：手法や結果を具体的な数値や事例と共に説明
````

:::

## Demo

実際に使用してみました。今回は，`code2vec`という論文のタイトルを入力しました。
![](/images/2024-12-05-claude-mcp-research/demo_01.png)

デスクトップのフォルダにファイルが保存されており，中身は下記のようになっていました。
![](/images/2024-12-05-claude-mcp-research/demo_02.png)

## 最後に

Claude Project の instructions を変更するだけで，出力形態をより高品質にすることができます。より良いプロンプトが見つかったら，ぜひコメントで教えてください！

## 参考文献

https://github.com/modelcontextprotocol/servers?tab=readme-ov-file
https://support.anthropic.com/ja/articles/9517075-%E3%83%97%E3%83%AD%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E3%81%A8%E3%81%AF%E4%BD%95%E3%81%A7%E3%81%99%E3%81%8B
https://note.com/teriyaki_ch/n/n77428e4462b6
