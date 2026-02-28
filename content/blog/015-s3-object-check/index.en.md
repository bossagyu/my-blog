+++
title = 'How to Check if an S3 Object Exists in Python boto3'
description = 'How to check if an S3 object exists using Python boto3. Covers the recommended head_object method, resource vs client differences, and common pitfalls.'
date = 2024-01-27T21:41:37+09:00
lastmod = 2024-01-27T21:41:37+09:00
draft = false
categories = ['Engineering']
tags = ['AWS', 'S3', 'Python', 'boto3']
+++

## Overview

This article explains how to check whether an S3 object exists using Python.
boto3 provides two API levels: `resource` and `client`. I'll cover both methods.

I used this approach when developing a LINE Bot where I needed to check if a user's configuration file existed in S3.

## resource vs client: Which Should You Use?

boto3 has two API levels:

| API | Characteristics | Best For |
|-----|-----------------|----------|
| `resource` | High-level API, object-oriented | Simple operations, readability |
| `client` | Low-level API, closer to AWS API | Fine-grained control, performance |

**Conclusion**: For simple existence checks, I recommend using `client`'s `head_object`. Here's why:

- `head_object` only retrieves object metadata, making it lightweight
- `resource`'s `load()` internally calls `head_object` anyway
- `client` is closer to the AWS API with more predictable behavior

## Using boto3.client (Recommended)

```python
import boto3
from botocore.exceptions import ClientError

def check_s3_object_exists(bucket_name: str, object_key: str) -> bool:
    """Check if an S3 object exists"""
    s3 = boto3.client('s3')
    try:
        s3.head_object(Bucket=bucket_name, Key=object_key)
        return True
    except ClientError as e:
        error_code = e.response['Error']['Code']
        if error_code == '404':
            return False
        # Re-raise non-404 errors (e.g., permission issues)
        raise

# Usage example
if check_s3_object_exists('my-bucket', 'path/to/file.json'):
    print("Object exists")
else:
    print("Object does not exist")
```

### Key Points

- `head_object` retrieves only metadata without downloading the object content
- Non-404 errors (e.g., 403: Access Denied) should be re-raised for proper handling
- Wrapping in a function makes it easier to test

## Using boto3.resource

If you prefer an object-oriented approach, this method also works:

```python
import boto3
from botocore.exceptions import ClientError

def check_s3_object_exists_resource(bucket_name: str, object_key: str) -> bool:
    """Check if an S3 object exists (resource version)"""
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

## Common Pitfalls

### 1. Confusing Permission Errors with 404

If you lack permissions to access the S3 bucket, you'll get a 403 error.
Catching only 404 and treating it as "doesn't exist" will mask permission issues.

```python
# BAD: Swallowing all errors
except ClientError:
    return False  # Permission errors also treated as "doesn't exist"

# GOOD: Re-raise non-404 errors
except ClientError as e:
    if e.response['Error']['Code'] == '404':
        return False
    raise  # Let caller handle permission errors
```

### 2. Using list_objects is Inefficient

You could use `list_objects` with prefix search, but it becomes slow with many objects.
For single object existence checks, use `head_object`.

## Summary

- Use `head_object` to check S3 object existence
- Handle non-404 errors properly (they may indicate permission issues)
- Both `resource` and `client` work, but `client` is simpler

## References

- [boto3 S3 head_object](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3/client/head_object.html)
- [Stack Overflow: check if a file exists in s3 bucket using boto3](https://stackoverflow.com/questions/33842944/check-if-a-key-exists-in-a-bucket-in-s3-using-boto3)

## Related Articles

- [Building a Cleaning Reminder Bot with AWS Lambda + LINE](/blog/046-clean-bot-technical/)
