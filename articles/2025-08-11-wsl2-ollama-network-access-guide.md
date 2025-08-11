---
title: "WSL2上のOllamaを同一ネットワークからアクセス可能にする完全ガイド"
emoji: "🧌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Ollama", "WSL2", "Windows", "ネットワーク", "ポートプロキシ"]
published: false
---

WSL2 環境で Ollama をインストールして他のデバイスから同一ネットワーク経由でアクセスしようとした際に、ローカル PC では接続できるが他の PC から接続できないという問題に遭遇しました。この記事では、WSL と Windows 間のネットワーク分離という根本的な問題と、その解決方法について詳しく解説します。

:::message
この記事の約 9 割は AI によって記述されています。実際に遭遇した問題と解決手順を基に、AI が技術記事として構成・執筆しました。
:::

## 使用環境

### ハードウェアスペック

- **デバイス**: Surface Pro 6
- **プロセッサー**: Intel(R) Core(TM) i7-8650U CPU @ 1.90GHz (2.11 GHz)
- **メモリ**: 8.00 GB
- **グラフィックス**: Intel(R) UHD Graphics 620 (128 MB)
- **ストレージ**: 238 GB

### 使用モデル

- **deepseek-r1:1.5b** - 軽量版で低スペック環境でも動作
- **phi3:3.8b** - マイクロソフト製の効率的な小型モデル

:::message alert
8GB メモリの環境では、軽量モデル（1.5B〜3.8B パラメータ）の使用を推奨します。より大きなモデルは動作が不安定になる可能性があります。
:::

:::message
**セキュリティに関する重要な注意**
この記事では例示用の IP アドレス（RFC 5737 準拠）を使用しています。実際の設定では、`ifconfig`や`hostname -I`で取得した実際の WSL IP アドレスを使用してください。本記事の IP アドレスをそのまま使用しないでください。
:::

## TL;DR

1. WSL2 のネットワーク分離を理解する
2. WSL の実際の IP アドレスを取得する
3. Windows ポートプロキシを設定して外部アクセスを可能にする
4. ファイアウォールの設定を行う
5. 接続テストを実施する

## 問題の本質を理解する

### WSL と Windows 間のネットワーク分離

**WSL2 は独立したネットワーク環境で動作する**ことが今回の問題の根本原因でした。

```
WSL2ネットワーク: 172.18.0.2（内部ネットワーク）
Windowsホスト: 192.0.2.10（実際のLANアドレス）
```

WSL2 は仮想的な内部ネットワークで動作するため、外部から直接アクセスすることができません。そのため、Windows ホスト側でポートプロキシを設定して、外部からのアクセスを WSL 内部に転送する必要があります。

### Ollama のリッスン設定の問題

最初は以下のような状態でした：

- 初期設定: `127.0.0.1:11434`（ローカルのみ）
- 修正後: `:::11434`（IPv6 ですべてのインターフェースでリッスン）

しかし、WSL 内部でのリッスン設定を変更しても、外部からは WSL 内部に直接アクセスできないため、さらなる設定が必要でした。

## 解決方法

### 1. WSL の実際の IP アドレスを取得

まず、WSL 内で Ollama が動作している IP アドレスを確認します。

```bash
# WSL内で実行
ifconfig
# または
hostname -I
```

例：`172.18.0.2`

### 2. Ollama の設定変更

WSL 内で Ollama がすべてのインターフェースでリッスンするように設定します。

```bash
# 環境変数を設定
export OLLAMA_HOST=0.0.0.0:11434
export OLLAMA_ORIGINS="*"

# Ollamaを起動
ollama serve
```

### 3. Windows ポートプロキシの設定

**正しい WSL IP でポートプロキシを設定**することが今回の問題解決の核心でした。

**管理者権限で PowerShell を開き**、以下のコマンドを実行します：

```powershell
# 既存のポートプロキシをクリア
netsh interface portproxy reset

# 正しいWSL IPでポートプロキシを設定
netsh interface portproxy add v4tov4 listenport=11434 listenaddress=0.0.0.0 connectport=11434 connectaddress=172.18.0.2

# 設定確認
netsh interface portproxy show all
```

正しく設定されると、以下のような出力が表示されるはずです：

```
Listen on ipv4:             Connect to ipv4:

Address         Port        Address         Port
--------------- ----------  --------------- ----------
0.0.0.0         11434       172.18.0.2      11434
```

#### ポートプロキシの設定内容

| パラメータ                  | 説明                                                         |
| --------------------------- | ------------------------------------------------------------ |
| `listenaddress=0.0.0.0`     | Windows の**すべてのネットワークインターフェース**でリッスン |
| `listenport=11434`          | Windows 側で**11434 ポート**を開放                           |
| `connectaddress=172.18.0.2` | **WSL の実際の IP アドレス**に転送                           |
| `connectport=11434`         | WSL 内の**11434 ポート**に転送                               |

#### 通信の流れ

```
他のPC (192.0.2.x)
    ↓ HTTPリクエスト
Windows PC (192.0.2.10:11434) ← ポートプロキシがここで受信
    ↓ 転送
WSL内のOllama (172.18.0.2:11434) ← 実際のOllamaサービス
```

### 4. ファイアウォール設定

Windows ファイアウォールで 11434 ポートを許可します：

```powershell
# 管理者権限でPowerShellで実行
New-NetFirewallRule -DisplayName "Ollama Port 11434" -Direction Inbound -Protocol TCP -LocalPort 11434 -Action Allow
```

### 5. 設定の確認

#### ポートプロキシ設定の確認

```powershell
# 現在のポートプロキシ設定を表示
netsh interface portproxy show v4tov4
```

#### ファイアウォール設定の確認

```powershell
# ファイアウォールルールの確認
Get-NetFirewallRule -DisplayName "Ollama Port 11434"
```

### 6. 接続テスト

#### ローカルでのテスト

```bash
# Windows側から
curl http://localhost:11434/api/version

# WSL側から
curl http://172.18.0.2:11434/api/version
```

**実行結果例：**

```bash
$ curl http://localhost:11434/api/version
{"version":"0.11.4"}

$ curl http://172.18.0.2:11434/api/version
{"version":"0.11.4"}
```

#### 他の PC からのテスト

```bash
# 同一ネットワークの他のPCから
curl http://192.0.2.10:11434/api/version
```

**実行結果例：**

```bash
$ curl http://192.0.2.10:11434/api/version
{"version":"0.11.4"}
```

:::message
上記の例では Ollama のバージョン 0.11.4 が正常に返されています。こちらのような応答が得られれば、外部からのアクセスが成功しています。
:::

## トラブルシューティング

### WSL IP アドレスが変わった場合

WSL2 は再起動時に IP アドレスが変更される可能性があります。

```powershell
# 古い設定を削除
netsh interface portproxy delete v4tov4 listenport=11434 listenaddress=0.0.0.0

# 新しいIPアドレスで再設定
netsh interface portproxy add v4tov4 listenport=11434 listenaddress=0.0.0.0 connectport=11434 connectaddress=[新しいWSL_IP]
```

### 接続できない場合のチェックポイント

1. **WSL で Ollama が起動しているか確認**

   ```bash
   ps aux | grep ollama
   ```

2. **WSL 内でのポート確認**

   ```bash
   netstat -tlnp | grep 11434
   ```

3. **Windows 側でのポート確認**

   ```powershell
   netstat -an | findstr 11434
   ```

4. **ファイアウォール設定の確認**
   ```powershell
   Get-NetFirewallRule -DisplayName "Ollama Port 11434" | Format-Table
   ```

## 重要な注意点

### セキュリティ設定

外部アクセスを許可する場合は、適切なセキュリティ設定を行ってください：

```bash
# 特定のオリジンのみ許可する場合（実際のネットワークアドレスを使用）
export OLLAMA_ORIGINS="http://192.168.1.0/24"  # 例：実際のローカルネットワーク

# 開発環境でのみすべて許可（本番環境では推奨しません）
export OLLAMA_ORIGINS="*"
```

:::message alert
**セキュリティリスク**

- `OLLAMA_ORIGINS="*"` は開発環境でのみ使用してください
- 本番環境では必ず特定のネットワーク範囲に制限してください
- ファイアウォール設定と併用して多層防御を行ってください
  :::

### IP アドレスの管理

WSL2 の IP アドレスは動的に変更される可能性があるため、以下のような対策を検討してください：

1. **固定 IP アドレスの設定**（上級者向け）
2. **起動スクリプトでの自動設定更新**
3. **定期的な手動確認**

## 最後に

この設定により、WSL2 上で動作する Ollama に同一ネットワーク内の他のデバイスからアクセスできるようになります。ただし、セキュリティ面での考慮は十分に行い、必要に応じてアクセス制限を設けることをお勧めします。

また、WSL2 のネットワーク仕様は今後のアップデートで変更される可能性があるため、定期的な設定確認を行うことも重要です。
