---
title: "Claude MCP can't connect to SQLite MCP serve を解決する方法"
emoji: "🧌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Claude", "MCP", "SQLite"]
published: true
---

## TL;DR

`Claude MCP can't connect to SQLite MCP serve` エラーが発生した場合の解決方法を紹介します。
私は下記のチュートリアルを行っている最中にこのエラーが発生しました。

https://modelcontextprotocol.io/quickstart

## 解決方法

絶対パスを使い、最新の Python バージョンを指定することで解決できます。

⚠️ 一通りチュートリアルは終わっていることを前提としています。

```json
{
  "mcpServers": {
    "sqlite": {
      "command": "uvx",
      "args": [
        "--python",
        "YourPythonPath",
        "mcp-server-sqlite",
        "--db-path",
        "/Users/YOUR_USERNAME/test.db"
      ]
    }
  }
}
```

上記の`YourPythonPath`を自分の環境に合わせて変更してください。
::: message
`which python` または `which python3` を実行して，パスを取得してください。
:::

それでも解決しない人は，`uvx`も絶対パスを指定してください。

## 参考文献

https://www.reddit.com/r/ClaudeAI/comments/1h0my0y/claude_mcp_cant_connect_to_sqlite_mcp_serve/
https://github.com/modelcontextprotocol/servers/issues/40#issuecomment-2500909192
