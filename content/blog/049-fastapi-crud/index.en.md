+++
title = 'Getting Started with FastAPI: Building a CRUD API Tutorial'
description = 'Learn how to build a TODO CRUD API with Python FastAPI framework. Covers Pydantic model definitions, endpoint implementation, and testing with Swagger UI.'
date = 2026-02-28T00:00:00+09:00
lastmod = 2026-02-28T00:00:00+09:00
draft = false
categories = ['Engineering']
tags = ['Python', 'FastAPI', 'API']
+++

## Overview

[FastAPI](https://fastapi.tiangolo.com/) is a high-performance web framework for building APIs with Python. Its key features include:

- **Type hint-based**: Leverages Python type hints for automatic request/response validation
- **Auto-generated documentation**: Swagger UI (OpenAPI) is automatically generated, allowing you to test APIs from the browser
- **High performance**: Built on Starlette + Pydantic, delivering performance comparable to Node.js and Go

In this article, we'll walk through building a TODO app CRUD API using FastAPI.

## Prerequisites

- Python 3.11 or higher
- uv installed (see [Setting Up a Python Development Environment on Mac with UV](/en/blog/032-python-uv/))

## Environment Setup

First, create a project and install the required packages.

```bash
uv init fastapi-todo
cd fastapi-todo
uv add fastapi uvicorn
```

`fastapi` is the web framework itself, and `uvicorn` is the ASGI server.

## Hello World

Let's start by verifying FastAPI works with a minimal setup. Create a `main.py` file.

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"message": "Hello, FastAPI!"}
```

Start the server:

```bash
uv run uvicorn main:app --reload
```

The `--reload` option automatically reloads the server when code changes are detected.

Open http://localhost:8000 in your browser and you'll see:

```json
{"message": "Hello, FastAPI!"}
```

### Swagger UI

One of FastAPI's greatest features is its auto-generated API documentation. Visit http://localhost:8000/docs to see the Swagger UI.

You can test APIs directly from here, eliminating the need for curl or Postman during development.

## Implementing the CRUD API

Let's implement the CRUD API for a TODO app.

### Defining Models

First, define the TODO data structure using Pydantic models. Replace `main.py` with the following:

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

- `TodoCreate`: Request body schema for creating and updating TODOs
- `TodoResponse`: Response type definition
- `todos`: In-memory data store (dict)
- `next_id`: Auto-incrementing ID counter

### Create (POST)

```python
@app.post("/todos", response_model=TodoResponse)
def create_todo(todo: TodoCreate):
    global next_id
    todo_data = {"id": next_id, "title": todo.title, "completed": todo.completed}
    todos[next_id] = todo_data
    next_id += 1
    return todo_data
```

By specifying `response_model=TodoResponse`, the response type is reflected in the Swagger UI.

### List (GET)

```python
@app.get("/todos", response_model=list[TodoResponse])
def list_todos():
    return list(todos.values())
```

### Get by ID (GET)

```python
@app.get("/todos/{todo_id}", response_model=TodoResponse)
def get_todo(todo_id: int):
    if todo_id not in todos:
        raise HTTPException(status_code=404, detail="Todo not found")
    return todos[todo_id]
```

Returns a 404 error via `HTTPException` when the ID doesn't exist.

### Update (PUT)

```python
@app.put("/todos/{todo_id}", response_model=TodoResponse)
def update_todo(todo_id: int, todo: TodoCreate):
    if todo_id not in todos:
        raise HTTPException(status_code=404, detail="Todo not found")
    todo_data = {"id": todo_id, "title": todo.title, "completed": todo.completed}
    todos[todo_id] = todo_data
    return todo_data
```

### Delete (DELETE)

```python
@app.delete("/todos/{todo_id}")
def delete_todo(todo_id: int):
    if todo_id not in todos:
        raise HTTPException(status_code=404, detail="Todo not found")
    del todos[todo_id]
    return {"detail": "Todo deleted"}
```

### Complete Code

Here's the full `main.py` with all endpoints combined:

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

## Testing

Start the server and test through the Swagger UI.

```bash
uv run uvicorn main:app --reload
```

Visit http://localhost:8000/docs and test in the following order:

### 1. Create a TODO

Open `POST /todos`, click "Try it out", and enter:

```json
{
  "title": "Go shopping",
  "completed": false
}
```

Click "Execute" to get a response like:

```json
{
  "id": 1,
  "title": "Go shopping",
  "completed": false
}
```

### 2. List TODOs

Execute `GET /todos` to see the list of created TODOs.

### 3. Update

Use `PUT /todos/1` to change `completed` to `true`:

```json
{
  "title": "Go shopping",
  "completed": true
}
```

### 4. Delete

Execute `DELETE /todos/1` to delete the TODO. Verify with `GET /todos` that an empty array is returned.

## Summary

In this article, we built a TODO CRUD API using FastAPI. Here are the key advantages:

- Automatic request/response validation with Pydantic models
- Interactive API documentation via Swagger UI
- Simple API specification definition using just Python type hints

We used an in-memory data store in this tutorial, but in real applications, you would use an ORM like SQLAlchemy to connect to a database.

## Related Articles

- [Setting Up a Python Development Environment on Mac with UV](/en/blog/032-python-uv/) - Python environment setup
- [Event-Driven Backend for Syncing GCS with Gemini File Search API](/en/blog/048-lawve-backend/) - Practical FastAPI example
