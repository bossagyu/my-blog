+++
title = 'Getting Started with an Interactive Help Bot using the OpenAI Assistants API'
date = 2025-12-21T21:39:59+09:00
draft = false
categories = ['Engineering']
tags = ['OpenAI', 'Assistants API', 'Python', 'API']
+++

## Overview
This article walks through building an interactive help bot with the OpenAI Assistants API. You’ll learn how to set up the Python client, upload project files, run threads, and stream responses while the assistant uses file search.

## Prerequisites
- Python 3.9 or later
- An OpenAI API key
- Ability to install packages with `pip`

## Setup
Create a project directory and install the required libraries:

```bash
python -m venv .venv
source .venv/bin/activate
pip install openai>=1.57.0 python-dotenv
```

Keep your API key in a `.env` file:

```bash
echo "OPENAI_API_KEY=sk-xxxxx" > .env
```

## Create an Assistant
Start by creating one assistant and enabling tools such as file search.

```python
# create_assistant.py
from openai import OpenAI
from dotenv import load_dotenv
load_dotenv()

client = OpenAI()

assistant = client.beta.assistants.create(
    name="Docs Helper",
    instructions="Reference the project documents and answer concisely.",
    # Use gpt-5.1-mini for speed/cost, or gpt-5.1 when you need higher quality
    model="gpt-5.1-mini",
    tools=[{"type": "file_search"}],
)

print(assistant.id)
```

## Upload files
Upload local manuals or source files so the assistant can search them.

```python
# upload_and_attach.py
from openai import OpenAI
from dotenv import load_dotenv
load_dotenv()

client = OpenAI()

file_ids = []
for path in ["README.md", "docs/guide.pdf"]:
    uploaded = client.files.create(file=open(path, "rb"), purpose="assistants")
    file_ids.append(uploaded.id)

assistant_id = "asst_xxx"  # Retrieved from create_assistant.py
client.beta.assistants.update(
    assistant_id=assistant_id,
    tool_resources={"file_search": {"file_ids": file_ids}},
)
```

## Create a thread and add a message
Store user questions in a thread before executing the run.

```python
# create_thread.py
from openai import OpenAI
from dotenv import load_dotenv
load_dotenv()

client = OpenAI()

thread = client.beta.threads.create(
    messages=[
        {"role": "user", "content": "How do I set up this repository?"},
    ]
)

print(thread.id)
```

## Run and stream responses
Use `beta.threads.runs.create_and_stream` to stream the answer and observe tool calls.

```python
# run_thread_stream.py
from openai import OpenAI
from dotenv import load_dotenv
load_dotenv()

client = OpenAI()

assistant_id = "asst_xxx"
thread_id = "thread_xxx"

with client.beta.threads.runs.create_and_stream(
    thread_id=thread_id,
    assistant_id=assistant_id,
    response_format={"type": "text"},
) as stream:
    for event in stream:
        if event.type == "text.delta":
            print(event.delta.value, end="", flush=True)
```

`text.delta` events contain streamed tokens, and `text.completed` signals the end. Tool invocation updates are also emitted on the same stream.

## Operational tips
- **Model choice:** Use `gpt-5.1-mini` for lower cost/latency and `gpt-5.1` when quality matters most; if a 4.x tier is sufficient, `gpt-4.1-mini`/`gpt-4.1` still works.
- **File granularity:** Split long manuals into smaller sections before uploading to improve search accuracy.
- **Access control:** Limit uploads that contain sensitive data and manage keys via environment variables or secret managers.
- **Rate limits:** Queue thread creation under heavy load and add backoff retries when you hit HTTP 429 responses.

## Summary
With file search and threaded runs, the Assistants API makes it straightforward to build a “docs-savvy help bot.” Start with a small set of project files, then expand the file set and tools as your use case grows.
