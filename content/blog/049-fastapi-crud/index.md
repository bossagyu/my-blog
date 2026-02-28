+++
title = 'FastAPIでCRUD APIを作る入門チュートリアル'
description = 'PythonのFastAPIフレームワークでTODOアプリのCRUD APIを作る方法を解説。Pydanticモデルの定義からSwagger UIでの動作確認まで、サンプルコード付きで紹介します。'
date = 2026-02-28T00:00:00+09:00
lastmod = 2026-02-28T00:00:00+09:00
draft = false
categories = ['Engineering']
tags = ['Python', 'FastAPI', 'API']
+++

## 概要

[FastAPI](https://fastapi.tiangolo.com/)は、Pythonで高性能なAPIを構築するためのWebフレームワークです。以下の特徴があります。

- **型ヒントベース**: Pythonの型ヒントを活用し、リクエスト・レスポンスの自動バリデーションを実現
- **自動ドキュメント**: Swagger UI（OpenAPI）が自動生成され、ブラウザからAPIをテスト可能
- **高速**: Starlette + Pydanticをベースにしており、Node.jsやGoに匹敵するパフォーマンス

この記事では、FastAPIを使ってTODOアプリのCRUD APIを作成する手順を解説します。

## 前提条件

- Python 3.11以上
- uvがインストール済み（[MacでUVを用いてPythonの開発環境を構築する](/blog/032-python-uv/)を参照）

## 環境構築

まず、プロジェクトを作成し、必要なパッケージをインストールします。

```bash
uv init fastapi-todo
cd fastapi-todo
uv add fastapi uvicorn
```

`fastapi`がWebフレームワーク本体、`uvicorn`がASGIサーバーです。

## Hello World

まずは最小構成でFastAPIの動作を確認します。`main.py`を作成してください。

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"message": "Hello, FastAPI!"}
```

サーバーを起動します。

```bash
uv run uvicorn main:app --reload
```

`--reload`オプションを付けると、コードの変更時に自動でリロードされます。

ブラウザで http://localhost:8000 にアクセスすると、以下のレスポンスが返ります。

```json
{"message": "Hello, FastAPI!"}
```

### Swagger UIの確認

FastAPIの大きな魅力は、自動生成されるAPIドキュメントです。http://localhost:8000/docs にアクセスすると、Swagger UIが表示されます。

ここからAPIを直接テストできるため、開発中はcurlやPostmanなしでも動作確認が可能です。

## CRUD APIの実装

TODOアプリのCRUD APIを実装していきます。

### モデルの定義

まず、PydanticモデルでTODOのデータ構造を定義します。`main.py`を以下のように書き換えてください。

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel


app = FastAPI()


class TodoCreate(BaseModel):
    title: str
    completed: bool = False


class TodoResponse(BaseModel):
    id: int
    title: str
    completed: bool


todos: dict[int, dict] = {}
next_id: int = 1
```

- `TodoCreate`: 新規作成・更新時のリクエストボディ
- `TodoResponse`: レスポンスの型定義
- `todos`: インメモリのデータストア（dict）
- `next_id`: IDの自動採番用カウンター

### 作成（POST）

```python
@app.post("/todos", response_model=TodoResponse)
def create_todo(todo: TodoCreate):
    global next_id
    todo_data = {"id": next_id, "title": todo.title, "completed": todo.completed}
    todos[next_id] = todo_data
    next_id += 1
    return todo_data
```

`response_model=TodoResponse`を指定することで、レスポンスの型がSwagger UIに反映されます。

### 一覧取得（GET）

```python
@app.get("/todos", response_model=list[TodoResponse])
def list_todos():
    return list(todos.values())
```

### 個別取得（GET）

```python
@app.get("/todos/{todo_id}", response_model=TodoResponse)
def get_todo(todo_id: int):
    if todo_id not in todos:
        raise HTTPException(status_code=404, detail="Todo not found")
    return todos[todo_id]
```

存在しないIDの場合は`HTTPException`で404エラーを返します。

### 更新（PUT）

```python
@app.put("/todos/{todo_id}", response_model=TodoResponse)
def update_todo(todo_id: int, todo: TodoCreate):
    if todo_id not in todos:
        raise HTTPException(status_code=404, detail="Todo not found")
    todo_data = {"id": todo_id, "title": todo.title, "completed": todo.completed}
    todos[todo_id] = todo_data
    return todo_data
```

### 削除（DELETE）

```python
@app.delete("/todos/{todo_id}")
def delete_todo(todo_id: int):
    if todo_id not in todos:
        raise HTTPException(status_code=404, detail="Todo not found")
    del todos[todo_id]
    return {"detail": "Todo deleted"}
```

### 完成したコード

上記をすべてまとめた`main.py`の全体像は以下のとおりです。

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel


app = FastAPI()


class TodoCreate(BaseModel):
    title: str
    completed: bool = False


class TodoResponse(BaseModel):
    id: int
    title: str
    completed: bool


todos: dict[int, dict] = {}
next_id: int = 1


@app.post("/todos", response_model=TodoResponse)
def create_todo(todo: TodoCreate):
    global next_id
    todo_data = {"id": next_id, "title": todo.title, "completed": todo.completed}
    todos[next_id] = todo_data
    next_id += 1
    return todo_data


@app.get("/todos", response_model=list[TodoResponse])
def list_todos():
    return list(todos.values())


@app.get("/todos/{todo_id}", response_model=TodoResponse)
def get_todo(todo_id: int):
    if todo_id not in todos:
        raise HTTPException(status_code=404, detail="Todo not found")
    return todos[todo_id]


@app.put("/todos/{todo_id}", response_model=TodoResponse)
def update_todo(todo_id: int, todo: TodoCreate):
    if todo_id not in todos:
        raise HTTPException(status_code=404, detail="Todo not found")
    todo_data = {"id": todo_id, "title": todo.title, "completed": todo.completed}
    todos[todo_id] = todo_data
    return todo_data


@app.delete("/todos/{todo_id}")
def delete_todo(todo_id: int):
    if todo_id not in todos:
        raise HTTPException(status_code=404, detail="Todo not found")
    del todos[todo_id]
    return {"detail": "Todo deleted"}
```

## 動作確認

サーバーを起動して、Swagger UIから動作確認を行います。

```bash
uv run uvicorn main:app --reload
```

http://localhost:8000/docs にアクセスし、以下の順にテストしてください。

### 1. TODOの作成

`POST /todos`を開き、「Try it out」をクリックして以下のJSONを入力します。

```json
{
  "title": "買い物に行く",
  "completed": false
}
```

「Execute」をクリックすると、以下のようなレスポンスが返ります。

```json
{
  "id": 1,
  "title": "買い物に行く",
  "completed": false
}
```

### 2. 一覧取得

`GET /todos`を実行すると、作成したTODOの一覧が返ります。

### 3. 更新

`PUT /todos/1`で`completed`を`true`に変更してみましょう。

```json
{
  "title": "買い物に行く",
  "completed": true
}
```

### 4. 削除

`DELETE /todos/1`を実行すると、TODOが削除されます。`GET /todos`で空の配列が返ることを確認してください。

## まとめ

この記事では、FastAPIを使ってTODO管理のCRUD APIを作成しました。FastAPIの主な利点をまとめます。

- Pydanticモデルによるリクエスト・レスポンスの自動バリデーション
- Swagger UIによるインタラクティブなAPIドキュメント
- Pythonの型ヒントだけでAPIの仕様が定義できるシンプルさ

今回はインメモリのデータストアを使いましたが、実際のアプリケーションではSQLAlchemyなどのORMを使ってデータベースと連携することになります。

## 関連記事

- [MacでUVを用いてPythonの開発環境を構築する](/blog/032-python-uv/) - Python環境構築
- [GCSとGemini File Search APIを自動同期するイベント駆動バックエンドの設計](/blog/048-lawve-backend/) - FastAPIの実践例
