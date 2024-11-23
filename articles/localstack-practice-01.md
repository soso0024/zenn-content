---
title: "LocalStackを使用して静的ウェブサイトを作成する"
emoji: "🚀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["aws", "localstack", "s3"]
publication_name: nislab
published: true
---

## TL;DR

今回は、LocalStack のインストール方法と、Local Stack で S3 を使用して静的ウェブサイトを作成する方法を紹介します。

<!-- 後半の記事では，Terraform を使用して Infrastructure as Code で構築する方法を紹介します。 -->

## 環境構築

今回は，macOS brew での環境構築を行います。
こちらの資料に，その他の OS での環境構築方法が記載されています。

https://docs.localstack.cloud/getting-started/installation/

### 1. LocalStack CLI のインストール

```bash
brew install localstack/tap/localstack-cli
```

正しくインストールされたか確認

```bash
localstack --version
```

### 2. 個人認証トークンの設定

LocalStack は、LocalStack ライセンスへのアクセスや、ワークスペース内での認証に個人認証トークンを使用します。

```bash
export LOCALSTACK_AUTH_TOKEN="YourAuthToken"
```

### 3. テスト環境の変数設定

```bash
export AWS_ACCESS_KEY_ID="test"
export AWS_SECRET_ACCESS_KEY="test"
export AWS_DEFAULT_REGION="us-east-1"
```

### 4. LocalStack の起動

注意：Docker が起動していることを確認してください。

```bash
localstack start
```

下記のようなログが表示されれば OK です。

```bash
     __                     _______ __             __
    / /   ____  _________ _/ / ___// /_____ ______/ /__
   / /   / __ \/ ___/ __ `/ /\__ \/ __/ __ `/ ___/ //_/
  / /___/ /_/ / /__/ /_/ / /___/ / /_/ /_/ / /__/ ,<
 /_____/\____/\___/\__,_/_//____/\__/\__,_/\___/_/|_|

 💻 LocalStack CLI 4.0.0
 👤 Profile: default

[12:47:13] starting LocalStack in Docker mode 🐳                       localstack.py:494
           preparing environment                                       bootstrap.py:1240
           configuring container                                       bootstrap.py:1248
           starting container                                          bootstrap.py:1258
[12:47:15] detaching
```

## 静的なウェブサイトの作成

原文はこちらです。英語が得意な方はこちらを参照してください。
https://docs.localstack.cloud/tutorials/s3-static-website-terraform/

### 1. `s3-static-website-localstack` （名前は任意）作業ディレクトリを作成する

```bash
mkdir s3-static-website-localstack
cd s3-static-website-localstack
```

### 2. `index.html` ファイルを作成する

こちらのファイルは、静的なウェブサイトのホームページとして表示されます。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta http-equiv="Content-Type" content="text/html" />
    <meta charset="utf-8" />
    <title>Static Website</title>
  </head>
  <body>
    <p>Static Website deployed locally over S3 using LocalStack</p>
  </body>
</html>
```

### 3. `error.html` ファイルを作成する

S3 は HTTP 4XX エラーコードに対してのみ、`error.html` のファイルコンテンツを返します。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>404</title>
  </head>
  <body>
    <p>Something is amiss.</p>
  </body>
</html>
```

## 静的なウェブサイトを S3 にホスティングする

S3 を使って静的ウェブサイトを作成するには、バケットを作成し、静的ウェブサイトのホスティングを有効にし、ファイルをバケットにアップロードする必要がある。 これらの操作には `awslocal CLI` を使用する。 プロジェクトのルート・ディレクトリに移動し、LocalStack の S3 API を使って `testwebsite` という名前のバケットを作成する：

`awscli` がインストールされていない場合は、以下のコマンドでインストールしてください。

```bash
pip install awscli
```

https://docs.localstack.cloud/user-guide/integrations/aws-cli/

### 1. バケットの作成

```bash
awslocal s3api create-bucket --bucket testwebsite
```

### 2. バケットのポリシー設定ファイルである`bucket_policy.json`作成

```bash
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::testwebsite/*"
        }
    ]
}
```

### 3. バケットポリシーをバケットにアタッチ

```bash
awslocal s3api put-bucket-policy --bucket testwebsite --policy file://bucket_policy.json
```

### 4. ポリシーが添付されたので、ルートディレクトリの内容をバケットに同期する

```bash
awslocal s3 sync ./ s3://testwebsite
```

### 5. バケット上で静的ウェブサイトホスティングを有効にし、インデックスドキュメントとエラードキュメントを設定する

```bash
awslocal s3 website s3://testwebsite/ --index-document index.html --error-document error.html
```

### 6. ブラウザで確認

LocalStack では、S3 ウェブサイトのエンドポイントは次の形式に従っています：http://<BUCKET_NAME>.s3-website.localhost.localstack.cloud:4566

```bash
http://testwebsite.s3-website.localhost.localstack.cloud:4566/
```

下記のような画面が表示されれば成功です。
![](/images/localstack-practice-01/static_web_contents.png)
