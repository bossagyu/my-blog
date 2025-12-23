+++
title = 'OpenAI Assistants APIで作る対話型ヘルプボット入門'
date = 2025-12-21T21:39:59+09:00
draft = false
categories = ['Engineering']
tags = ['OpenAI', 'Assistants API', 'Python', 'API']
+++

## 概要
OpenAI Assistants APIを使い、ローカルのコードやマニュアルを参照しながら回答する対話型ヘルプボットを作る手順をまとめます。Pythonクライアントでのセットアップ、ファイルアップロード、スレッド実行、ストリーミング取得までを通して一連の流れを確認します。

## 前提条件
- Python 3.9以降がインストールされていること
- OpenAI APIキーが取得済みであること
- `pip`でパッケージを追加できる環境

## セットアップ
プロジェクトディレクトリを用意し、必要なライブラリをインストールします。

```bash
python -m venv .venv
source .venv/bin/activate
pip install openai>=1.57.0 python-dotenv
```

`.env`にAPIキーを記載しておくと安全です。

```bash
echo "OPENAI_API_KEY=sk-xxxxx" > .env
```

## Assistantの作成
まずはアシスタントを1つ作成します。ツールとしてコード実行やファイル検索を有効にできます。

```python
# create_assistant.py
from openai import OpenAI
from dotenv import load_dotenv
load_dotenv()

client = OpenAI()

assistant = client.beta.assistants.create(
    name="Docs Helper",
    instructions="プロジェクトのドキュメントを参照し、簡潔に回答してください。",
    # 高速・軽量なら gpt-5.1-mini、精度優先なら gpt-5.1 など 5 系を選ぶ
    model="gpt-5.1-mini",
    tools=[{"type": "file_search"}],
)

print(assistant.id)
```

## ファイルのアップロード
ローカルのマニュアルやサンプルコードを検索対象にするには、ファイルをアップロードしてアシスタントに紐付けます。

```python
# upload_and_attach.py
from openai import OpenAI
from dotenv import load_dotenv
load_dotenv()

client = OpenAI()

# PDFやMarkdownなど複数ファイルをアップロード
file_ids = []
for path in ["README.md", "docs/guide.pdf"]:
    uploaded = client.files.create(file=open(path, "rb"), purpose="assistants")
    file_ids.append(uploaded.id)

# 既存アシスタントにファイル検索リソースを設定
assistant_id = "asst_xxx"  # create_assistant.pyで取得したID
client.beta.assistants.update(
    assistant_id=assistant_id,
    tool_resources={"file_search": {"file_ids": file_ids}},
)
```

## スレッドを作成しメッセージを送る
ユーザーの質問はスレッドに溜め、実行時にまとめて処理します。

```python
# create_thread.py
from openai import OpenAI
from dotenv import load_dotenv
load_dotenv()

client = OpenAI()

thread = client.beta.threads.create(
    messages=[
        {"role": "user", "content": "このリポジトリのセットアップ手順を教えて"},
    ]
)

print(thread.id)
```

## 実行とストリーミング受信
`beta.threads.runs.create_and_stream`を使うと、回答を逐次受信できます。ツール呼び出しが発生した場合のイベントも同じストリームで受け取れます。

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

`text.delta`でトークンが届き、最終的に`text.completed`で終了します。ファイル検索やツール呼び出しの進捗もイベントで確認できます。

## 補足: 運用時のポイント
- **モデル選択:** コストと速度を重視するなら`gpt-5.1-mini`、精度重視なら`gpt-5.1`を使い分けます。4系で十分な場合は`gpt-4.1-mini`/`gpt-4.1`でも動作します。
- **ファイルの粒度:** マニュアルは章ごとに分割してアップロードすると検索精度が上がります。
- **権限管理:** 秘匿情報を含む場合はアップロード対象を絞り、キー管理は環境変数やシークレットマネージャーで行います。
- **レート制限:** 同時実行が多い場合はスレッド生成をキューイングし、HTTP 429が返ったらリトライバックオフを入れると安定します。

## まとめ
Assistants APIでは、ファイル検索とスレッド実行を組み合わせるだけで「社内ドキュメントに強いヘルプボット」が作れます。まずは小規模なリポジトリから試し、必要に応じてファイルやツールを拡張するとスムーズです。
