+++
title = 's3のオブジェクトの存在確認をする方法'
date = 2024-01-27T21:41:37+09:00
draft = false
categories = ['Engineering']
tags = ['Hugo', 'Twitter', 'Social Card']
+++

## 概要
このページではPythonでs3のオブジェクトの存在確認をする方法を説明します。

## boto3を利用して確認する方法

boto3.resourceを利用する場合は以下のようなコードでチェックできます。
```python
s3 = boto3.resource('s3')
try:
    s3.Object('bucket_name', 'object_name').load()
    print("True")
except ClientError as e:
    error_code = e.response['Error']['Code']
    if error_code == '404':
        print("Object does not exist.")
    else:
        print(f"An error occurred: {e}")
```

boto3.clientを利用する場合は以下のようなコードでチェックできます。
```python
s3 = boto3.client('s3')
try:
    s3.head_object(Bucket='bucket_name', Key='object_name')
    print("True")
except ClientError as e:
    error_code = e.response['Error']['Code']
    if error_code == '404':
        print("Object does not exist.")
    else:
        print(f"An error occurred: {e}")
```


## 参考
* [check if a file exists in s3 bucket using boto3](https://stackoverflow.com/questions/33842944/check-if-a-key-exists-in-a-bucket-in-s3-using-boto3)