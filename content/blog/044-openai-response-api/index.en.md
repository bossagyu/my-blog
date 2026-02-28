+++
title = 'How to Use the OpenAI Response API'
description = 'How to use the OpenAI Response API. Covers API key setup, Python text generation, and tool calling with minimal code examples to get started.'
date = 2025-12-24T10:00:00+09:00
lastmod = 2025-12-24T10:00:00+09:00
draft = false
categories = ['Engineering']
tags = ['OpenAI', 'ResponseAPI', 'Python', 'API']
+++

## Overview
This article summarizes how to use the OpenAI Response API for text generation. It covers the official documentation, how to obtain an API key, and a minimal Python example.

## What is the Response API?
The Response API is OpenAI's unified API for text generation, tool calls, and multi-turn interactions. It works for both one-off requests and conversational workflows.

## Official documentation
The most reliable source for the latest specs is the official documentation.

- Response API reference: <https://platform.openai.com/docs/api-reference/responses>

## How to get an API key
You need an OpenAI API key to use the Response API. Follow these steps:

1. Sign in to the OpenAI dashboard
2. Open the API Keys page: <https://platform.openai.com/api-keys>
3. Create a new key with “Create new secret key”

Store the key in an environment variable for safety.

```bash
export OPENAI_API_KEY="<YOUR_API_KEY>"
```

## Python sample code
Install the Python SDK with uv:

```bash
uv pip install openai
```

Below is a minimal sample that calls the Response API.

```python
from openai import OpenAI

client = OpenAI()

response = client.responses.create(
    model="gpt-4.1-mini",
    input="Summarize the key features of the Response API in 3 lines.",
)

print(response.output_text)
```

### Example output

```text
The Response API is a unified endpoint for one-off and conversational generation.
It lets you retrieve model output with a simple request structure.
Start with the docs and expand as your use case grows.
```

## Summary
The Response API is a straightforward way to generate text with OpenAI. Check the docs, prepare an API key, and try the Python SDK for a smooth start.

## Related Articles

- [Designing an Event-Driven Backend to Sync GCS with Gemini File Search API](/en/blog/048-lawve-backend/) (a practical example of API integration patterns)
