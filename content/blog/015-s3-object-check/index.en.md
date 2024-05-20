+++
title = 'Checking for the Existence of an Object in S3'
date = 2024-01-27T21:41:37+09:00
draft = true
categories = ['Engineering']
tags = ['Python', 'AWS', 'S3']
+++

## Overview
This page explains how to check for the existence of an object in S3 using Python.

## Method Using boto3

To check using `boto3.resource`, you can use the following code:
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

If you're using `boto3.client`, you can check with this code:
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

## Reference
* [check if a file exists in s3 bucket using boto3](https://stackoverflow.com/questions/33842944/check-if-a-key-exists-in-a-bucket-in-s3-using-boto3)