---
title: "Terraformを使用してインフラストラクチャの自動化"
emoji: "🌍"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["aws", "localstack", "terraform", "infrastructure-as-code"]
publication_name: nislab
published: true
---

### 注意 ⚠️：この記事は前回の記事の続きです。

https://zenn.dev/nislab/articles/localstack-practice-01

## TD;LR

前回の記事で作成した，静的ウェブサイトを Terraform を使用して Infrastructure as Code で構築する方法を紹介します。

## 私たちの研究室

https://nisk.doshisha.ac.jp/

## Terraform とは

- インフラストラクチャをコードとして管理（IaC）するツール
- インフラの作成、管理、バージョン管理が可能
- HashiCorp Configuration Language（HCL）という宣言的な設定言語を使用

## Terraform を使ったインフラのオーケストレーション

Terraform を使って AWS インフラをオーケストレーションすることで、上記のプロセスを自動化していきます。

### 1. AWS Provider の設定

1. `provider.tf` ファイルを作成します。
2. 以下の内容を記述して AWS プロバイダーの設定を行います：

```hcl
provider "aws" {
    region      = "us-east-1"
    access_key  = "fake"
    secret_key  = "fake"
}
```

:::details 設定内容の説明

- **region**: AWS のリージョンを指定（ここでは"us-east-1"を使用）
- **access_key**: LocalStack 用のモックアクセスキー（"fake"を使用）
- **secret_key**: LocalStack 用のモックシークレットキー（"fake"を使用）
  :::

この設定により、Terraform が LocalStack 上で AWS リソースを操作できるようになります。実際の AWS 認証情報は必要なく、ローカル環境でのテストが可能です。

### 2. LocalStack の詳細設定：provider.tf の構成

LocalStack で AWS サービスを使用するために、`provider.tf`にいくつかの重要な設定を追加する必要があります。以下に詳細を説明します。

1.  基本設定

```hcl
provider "aws" {
    access_key = "test"
    secret_key = "test"
    region     = "us-east-1"
```

2.  セキュリティとバリデーション設定

```hcl
    # LocalStack環境用の最適化設定
    s3_use_path_style            = false
    skip_credentials_validation  = true
    skip_metadata_api_check     = true
    skip_requesting_account_id  = true
```

3. エンドポイント設定

```hcl
    endpoints {
        s3 = "http://s3.localhost.localstack.cloud:4566"
    }
}
```

:::details 設定の詳細

#### 認証設定

- **access_key**, **secret_key**: ローカル環境用のテスト認証情報
- **region**: 使用する AWS リージョン

#### セキュリティ設定

- **s3_use_path_style**: S3 のパススタイルアクセス設定
- **skip_credentials_validation**: 認証情報の検証をスキップ
- **skip_metadata_api_check**: メタデータ API チェックをスキップ
- **skip_requesting_account_id**: アカウント ID の要求をスキップ

#### サービスエンドポイント

- **endpoints**: 各 AWS サービスのローカルエンドポイントを指定
  - S3 サービスは`4566`ポートで提供

:::

これらの設定により、LocalStack 環境で AWS サービスを安全にエミュレートすることができます。

:::details エミュレートとは
エミュレート（Emulate）とは、以下のような意味があります：

- あるシステムを別のシステムで模倣・再現すること
- 本物の AWS 環境を、ローカル PC 上で疑似的に動作させること
- 実際のクラウド環境を使わずに、同じような機能をローカルで再現・模擬すること

簡単な例えで言うと、「本番の AWS 環境のモノマネ」をローカル環境で行うことです。これにより、実際の AWS を使わずにテストや開発が可能になります。
:::

### 3. S3 バケットの変数設定：variables.tf の構成

S3 バケットの設定に必要な変数を`variables.tf`ファイルで定義します。以下に詳細を説明します。

1. バケット名の設定

```hcl
variable "bucket_name" {
    description = "Name of the s3 bucket. Must be unique."
    type        = string
}
```

2. タグ設定の定義

```hcl
variable "tags" {
    description = "Tags to set on the bucket."
    type        = map(string)
    default     = {}
}
```

:::details 設定の詳細

#### バケット名の変数（bucket_name）

- **description**: バケットの説明（一意である必要がある）
- **type**: 文字列型として定義

#### タグの変数（tags）

- **description**: バケットに設定するタグの説明
- **type**: 文字列のマップ型として定義
- **default**: デフォルト値は空のマップ

:::

これらの変数設定により、S3 バケットの名前とタグを柔軟に管理することができます。変数を使用することで、異なる環境や用途に応じて設定を容易に変更できます。

:::details 変数設定の利点

- 設定の再利用性が高まる
- 環境ごとの設定変更が容易
- コードの保守性が向上
- 設定ミスのリスクを軽減

:::

### 4. 出力変数の設定：outputs.tf の構成

Terraform の実行結果として出力する変数を`outputs.tf`ファイルで定義します。以下に詳細を説明します。

1. バケットの ARN 出力

```hcl
output "arn" {
    description = "ARN of the bucket"
    value       = aws_s3_bucket.s3_bucket.arn
}
```

2. バケット名（ID）の出力

```hcl
output "name" {
    description = "Name (id) of the bucket"
    value       = aws_s3_bucket.s3_bucket.id
}
```

3. ドメイン名の出力

```hcl
output "domain" {
    description = "Domain name of the bucket"
    value       = aws_s3_bucket_website_configuration.s3_bucket.website_domain
}
```

4. ウェブサイトエンドポイントの出力

```hcl
output "website_endpoint" {
    value = aws_s3_bucket_website_configuration.s3_bucket.website_endpoint
}
```

:::details 出力変数の詳細説明

#### ARN 出力

- S3 バケットの ARN（Amazon Resource Name）を出力
- AWS 内でリソースを一意に識別するために使用

#### バケット名出力

- 作成された S3 バケットの名前（ID）を出力
- バケットへのアクセスや参照に使用

#### ドメイン名出力

- S3 バケットのウェブサイトドメイン名を出力
- 静的ウェブサイトホスティングで使用

#### エンドポイント出力

- S3 バケットのウェブサイトエンドポイント URL を出力
- ウェブサイトへのアクセスに使用

:::

これらの出力変数により、作成した S3 バケットの重要な情報を確認することができます。

### 5. S3 バケットの作成：main.tf の構成

全ての設定ファイルを作成した後、`main.tf`で S3 バケットを定義します。以下に詳細を説明します。

1. S3 バケットリソースの定義

```hcl
resource "aws_s3_bucket" "s3_bucket" {
    bucket = var.bucket_name
    tags   = var.tags
}
```

:::details リソース定義の詳細説明

#### リソース名の構成

- **"aws_s3_bucket"**: AWS の S3 バケットを作成するためのリソースタイプ
- **"s3_bucket"**: このリソースの参照名（ローカルでの識別子）

#### 設定パラメータ

- **bucket**: `variables.tf`で定義したバケット名変数を使用
- **tags**: `variables.tf`で定義したタグ変数を使用

#### 変数参照方法

- `var.変数名`の形式で変数を参照
- 例：`var.bucket_name`, `var.tags`
  :::

これで以下の構成ファイルが揃い、S3 バケットを作成する準備が整いました：

- `provider.tf`: AWS 接続設定
- `variables.tf`: 入力変数定義
- `outputs.tf`: 出力変数定義
- `main.tf`: S3 バケットリソース定義

:::details Terraform のファイル構成について

- 各設定ファイルは役割ごとに分かれており、コードの管理が容易
- 変数を使用することで、環境に応じた設定変更が可能
- アウトプットにより、作成したリソースの情報を簡単に確認可能
  :::

### 6. S3 バケットの静的ウェブサイト設定：main.tf の追加設定

`main.tf`ファイルに静的ウェブサイトホスティングの設定とバケットポリシーを追加します。以下に詳細を説明します。

1. 静的ウェブサイト設定

```hcl
resource "aws_s3_bucket_website_configuration" "s3_bucket" {
    bucket = aws_s3_bucket.s3_bucket.id

    index_document {
        suffix = "index.html"
    }

    error_document {
        key = "error.html"
    }
}
```

2. バケットのアクセス制御とポリシー設定

```hcl
resource "aws_s3_bucket_acl" "s3_bucket" {
    bucket = aws_s3_bucket.s3_bucket.id
    acl    = "public-read"
}

resource "aws_s3_bucket_policy" "s3_bucket" {
    bucket = aws_s3_bucket.s3_bucket.id

    policy = jsonencode({
        Version = "2012-10-17"
        Statement = [
            {
                Sid       = "PublicReadGetObject"
                Effect    = "Allow"
                Principal = "*"
                Action    = "s3:GetObject"
                Resource  = [
                    aws_s3_bucket.s3_bucket.arn,
                    "${aws_s3_bucket.s3_bucket.arn}/*",
                ]
            },
        ]
    })
}
```

:::details 設定の詳細説明

#### ウェブサイト設定

- **index_document**: デフォルトページとして表示するファイル
- **error_document**: エラー発生時に表示するファイル

#### アクセス制御設定

- **acl**: パブリックな読み取りアクセスを許可
- **bucket_policy**: より詳細なアクセス制御を定義
  - すべてのユーザーにオブジェクトの取得を許可
  - バケット全体とその中のすべてのオブジェクトに適用

:::

これらの設定により、S3 バケットで静的ウェブサイトをホスティングし、パブリックにアクセス可能な状態で公開することができます。

### 7. S3 バケットへのファイルアップロード設定：main.tf の追加設定

`main.tf`ファイルにファイルアップロードの設定を追加します。HTML ファイルと静的アセットの両方をアップロードする設定を説明します。

1. HTML ファイルのアップロード設定

```hcl
resource "aws_s3_object" "object_www" {
    depends_on    = [aws_s3_bucket.s3_bucket]
    for_each      = fileset("${path.root}", "*.html")
    bucket        = var.bucket_name
    key          = basename(each.value)
    source        = each.value
    etag         = filemd5("${each.value}")
    content_type = "text/html"
    acl          = "public-read"
}
```

2. 静的アセット（CSS, JS, 画像など）のアップロード設定

```hcl
resource "aws_s3_object" "object_assets" {
    depends_on = [aws_s3_bucket.s3_bucket]
    for_each   = fileset(path.module, "assets/*")
    bucket     = var.bucket_name
    key        = each.value
    source     = "${each.value}"
    etag      = filemd5("${each.value}")
    acl       = "public-read"
}
```

:::details 設定項目の説明

#### 共通パラメータ

- **depends_on**: バケット作成後に実行されるよう依存関係を設定
- **bucket**: アップロード先のバケット名
- **acl**: パブリックな読み取りアクセスを許可

#### HTML ファイル用の設定

- **for_each**: ルートディレクトリの全 HTML ファイルを対象
- **content_type**: HTML として認識されるよう設定
- **key**: ファイル名のみを使用（パスなし）

#### 静的アセット用の設定

- **for_each**: assets/ディレクトリ内の全ファイルを対象
- **key**: assets/を含むパスを維持

:::

これらの設定により、ウェブサイトに必要な全ファイルを S3 バケットに自動的にアップロードすることができます。

### 8. Terraform の実行手順：S3 バケットのデプロイ

LocalStack 環境で S3 バケットを作成するための Terraform コマンドの実行手順を説明します。

1. Terraform の初期化

```bash
$ terraform init
```

- Terraform の設定を初期化
- 必要なプロバイダーをダウンロード

2. 実行プランの作成

```bash
$ terraform plan
```

- 作成されるリソースの確認
- 変更内容のプレビュー表示

3. 設定の適用

```bash
$ terraform apply
```

実行結果として以下の情報が出力されます：

```
var.bucket_name
  Name of the s3 bucket.
  Must be unique.

Enter a value: testbucket

...
arn = "arn:aws:s3:::testbucket"
domain = "s3-website-us-east-1.amazonaws.com"
name = "testbucket"
website_endpoint = "testbucket.s3-website-us-east-1.amazonaws.com"
```

:::details 出力情報の説明

- **arn**: バケットの Amazon Resource Name
- **domain**: S3 ウェブサイトのドメイン
- **name**: バケット名
- **website_endpoint**: ウェブサイトのエンドポイント（LocalStack 環境を使用しているため、この URL ではアクセスできない）

:::

:::details LocalStack での注意点

- バケット名は自由に指定可能（LocalStack は一時的な環境のため）
- アクセス URL: `http://<BUCKET_NAME>.s3-website.localhost.localstack.cloud:4566`
- 実際の AWS リソースは作成されない

:::

これらのコマンドを実行することで、LocalStack 環境に S3 バケットが作成され、静的ウェブサイトホスティングが完了します。
