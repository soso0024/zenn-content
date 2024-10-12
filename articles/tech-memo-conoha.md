---
title: "ConoHa使ってSSH接続しただけ"
emoji: "🍃"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["ConoHa", "SSH", "VPS"]
published: true
---

ConoHa 使って SSH 接続しただけのメモ．

## VPS の作成

1. [ConoHa](https://www.conoha.jp/) にアクセスして，アカウントを作成する．
2. サービスは「VPS」を選択する．
   ![](/images/tech-memo-conoha/conoha_CSL.png)
3. イメージタイプ，料金タイプは任意で選択する．
4. 追加をクリックする．

## セキュリティグループの設定

1. 左側のメニューから「セキュリティ」→「セキュリティグループ」を選択する．
2. 「セキュリティグループの作成」をクリックする．
3. 名前は任意で設定する．
4. 説明は任意で設定する．
5. 下記の設定を追加
   | 通信方式 | イーサタイプ | プロトコル | ポート範囲 | IP／CIDR |
   | -------- | ------------ | ---------- | ---------- | --------- |
   | In | イータタイプ | TCP | 22 | 0.0.0.0/0 |
   - 22 は SSH のポート番号
   - 0.0.0.0/0 は全ての IP アドレスを表す．
   - アクセス元の IP アドレスを指定したい場合は，IP アドレスを指定する．
     - [こちらのサイト](https://www.whatismyip.com/)から IP アドレスを確認する．
     - /32 を付けることで，単一の IP アドレスを指定できる．
6. 左側のメニューから「サーバー」→「VPS」を選択する．
7. ネットワーク情報タブを展開し，先ほど作成したセキュリティグループを選択する．
8. サーバーの IP アドレスをメモする．（SSH 接続時に使用）

## SSH 接続

1. ターミナルを開き，SSH 接続する．
   ```bash
   ssh root@<サーバーの IP アドレス>
   ```
2. パスワードを入力する．
3. 信頼されていないホストの警告が出るので，「yes」をクリックする．
4. 完了