+++
title = 'S3のオブジェクトの存在確認をする方法【Python boto3】'
description = 'Python boto3でS3オブジェクトの存在確認をする方法を解説。head_objectを使った推奨方法、resourceとclientの違い、よくあるハマりポイントを紹介します。'
date = 2024-01-27T21:41:37+09:00
lastmod = 2024-01-27T21:41:37+09:00
draft = false
categories = ['Engineering']
tags = ['AWS', 'S3', 'Python', 'boto3']
+++

## 概要

PythonでS3のオブジェクトが存在するかどうかを確認する方法を説明します。
boto3には`resource`と`client`の2つのAPIがあり、それぞれの方法を紹介します。

実際にLINE Botの開発で、ユーザーごとの設定ファイルがS3に存在するかチェックする必要があり、この方法を使いました。

## resource vs client どちらを使うべきか

boto3には2つのAPIレベルがあります。

| API | 特徴 | 適したユースケース |
|-----|------|-------------------|
| `resource` | 高レベルAPI、オブジェクト指向 | シンプルな操作、可読性重視 |
| `client` | 低レベルAPI、AWS APIに近い | 細かい制御、パフォーマンス重視 |

**結論**: 単純な存在確認なら`client`の`head_object`がおすすめです。理由は以下の通りです。

- `head_object`はオブジェクトのメタデータのみを取得するため軽量
- `resource`の`load()`も内部的には`head_object`を呼んでいる
- `client`の方がAWS APIに近く、挙動が明確

## boto3.clientを利用する方法（推奨）

```python
import boto3
from botocore.exceptions import ClientError

def check_s3_object_exists(bucket_name: str, object_key: str) -> bool:
    """S3オブジェクトの存在確認"""
    s3 = boto3.client('s3')
    try:
        s3.head_object(Bucket=bucket_name, Key=object_key)
        return True
    except ClientError as e:
        error_code = e.response['Error']['Code']
        if error_code == '404':
            return False
        # 404以外のエラー（権限不足など）は再raise
        raise

# 使用例
if check_s3_object_exists('my-bucket', 'path/to/file.json'):
    print("オブジェクトが存在します")
else:
    print("オブジェクトが存在しません")
```

### ポイント

- `head_object`はオブジェクトの内容をダウンロードせず、メタデータのみを取得
- 404エラー以外（403: アクセス拒否など）は別のエラーなので再raiseする
- 関数化しておくとテストしやすい

## boto3.resourceを利用する方法

オブジェクト指向的に書きたい場合はこちらも使えます。

```python
import boto3
from botocore.exceptions import ClientError

def check_s3_object_exists_resource(bucket_name: str, object_key: str) -> bool:
    """S3オブジェクトの存在確認（resource版）"""
    s3 = boto3.resource('s3')
    try:
        s3.Object(bucket_name, object_key).load()
        return True
    except ClientError as e:
        error_code = e.response['Error']['Code']
        if error_code == '404':
            return False
        raise
```

## よくあるハマりポイント

### 1. 権限不足を404と間違える

S3バケットへのアクセス権限がない場合、403エラーが返ります。
404だけをキャッチして「存在しない」と判断すると、権限の問題を見逃します。

```python
# NG: 404以外のエラーを握りつぶしている
except ClientError:
    return False  # 権限エラーも「存在しない」扱いになる

# OK: 404以外は再raise
except ClientError as e:
    if e.response['Error']['Code'] == '404':
        return False
    raise  # 権限エラーなどは呼び出し元で処理
```

### 2. list_objectsを使う方法は非効率

`list_objects`でプレフィックス検索する方法もありますが、オブジェクト数が多いと遅くなります。
単一オブジェクトの存在確認には`head_object`を使いましょう。

## まとめ

- S3オブジェクトの存在確認には`head_object`を使う
- 404エラー以外は権限不足などの可能性があるので適切に処理する
- `resource`と`client`どちらでも可能だが、`client`の方がシンプル

## 参考

- [boto3 S3 head_object](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3/client/head_object.html)
- [Stack Overflow: check if a file exists in s3 bucket using boto3](https://stackoverflow.com/questions/33842944/check-if-a-key-exists-in-a-bucket-in-s3-using-boto3)