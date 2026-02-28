+++
title = 'GCSとGemini File Search APIを自動同期するイベント駆動バックエンドの設計'
description = 'Google Cloud StorageのファイルをEventarc経由でGemini File Search APIと自動同期するバックエンドをFastAPIで構築。イベント駆動アーキテクチャの設計思想と汎用性を持たせた実装のポイントを解説します。'
date = 2026-02-15T00:00:00+09:00
lastmod = 2026-02-15T00:00:00+09:00
draft = false
categories = ['Engineering']
tags = ['Python', 'GCP', 'Gemini', 'FastAPI']
+++

## 概要

デジタル庁主催の「法令×デジタル」ハッカソン第三弾に参加し、法令横断検索プロダクト「Lawve」を開発しました。Lawveは、e-Gov法令データやユーザーがアップロードしたドキュメントを横断的に自然言語検索できるプロダクトです。

この記事では、筆者が担当したバックエンドのアーキテクチャ設計について解説します。バックエンドの主な責務は「Google Cloud Storage（GCS）にファイルが配置・削除されたとき、Gemini File Search APIの状態を自動的に同期する」ことです。

## システム全体像

Lawveのシステム構成は以下の通りです。

```mermaid
flowchart TB
    subgraph ユーザー
        U[ブラウザ]
    end

    subgraph GCP
        subgraph フロントエンド
            FE[Cloud Run<br/>Next.js]
        end

        subgraph バックエンド
            CR[Cloud Run<br/>FastAPI]
        end

        subgraph ストレージ・データ
            GCS[(Cloud Storage)]
            FS[(Firestore)]
        end

        subgraph イベント基盤
            EA[Eventarc]
        end

        subgraph AI
            GEMINI[Gemini File<br/>Search API]
        end
    end

    subgraph 外部API
        EGOV[e-Gov<br/>法令API]
    end

    U --> FE
    FE --> GEMINI
    FE --> FS
    FE --> EGOV
    FE -->|ファイルアップロード| GCS

    GCS -->|イベント通知| EA
    EA -->|CloudEvents| CR
    CR -->|登録・削除| GEMINI
    CR -->|ダウンロード| GCS
```

### 各コンポーネントの役割

| コンポーネント | 役割 |
|---|---|
| Cloud Run（Next.js） | フロントエンド。検索UI、ファイルアップロード、検索結果の表示 |
| Cloud Run（FastAPI） | バックエンド。GCSのイベントを受けてGemini File Search APIを同期 |
| Cloud Storage | ドキュメントの保管場所。Single Source of Truth |
| Eventarc | GCSのファイル変更イベントをCloud Runにルーティング |
| Gemini File Search API | ドキュメントの全文検索・セマンティック検索を提供 |
| Firestore | ドキュメントメタデータ、コメント情報の管理 |
| e-Gov法令API | 法令データの取得 |

この記事では、バックエンドの Cloud Run（FastAPI）の設計に焦点を当てます。

## イベント駆動アーキテクチャの設計

### なぜイベント駆動を選んだか

法令検索プロダクトでは、ドキュメントがGCSに配置されたタイミングでGemini File Search APIに自動登録される必要があります。フロントエンドからの同期APIを作る方法もありますが、以下の理由でイベント駆動アーキテクチャを採用しました。

- **疎結合**: フロントエンドはGCSにファイルを配置するだけでよく、バックエンドの存在を意識しない
- **信頼性**: GCSのイベントをEventarcが確実に検知し、Cloud Runに配信する
- **運用ツールとの親和性**: CLIやスクリプトからGCSにファイルを配置しても同じように同期される

### Eventarcによるイベントルーティング

Eventarcは2種類のGCSイベントを監視し、Cloud Runのエンドポイントにルーティングします。

| イベント | トリガー条件 |
|---|---|
| `google.cloud.storage.object.v1.finalized` | ファイルがGCSにアップロードされたとき |
| `google.cloud.storage.object.v1.deleted` | ファイルがGCSから削除されたとき |

Cloud RunのエンドポイントはCloudEventsフォーマットでイベントを受信します。

```python
@app.post("/")
async def handle_storage_event(request: Request):
    headers = dict(request.headers)
    body = await request.body()
    event = from_http(headers, body)

    result = event_handler.process(event)
    return result
```

### ファイルアップロード時の処理フロー

ファイルがGCSに配置されると、以下のフローで処理されます。

1. **イベント受信・パスフィルタリング**: `file-search/archive/` プレフィックスを持つファイルのみ処理対象とする
2. **メタデータ抽出**: GCSパスから `law_id` と `source_type` を自動抽出
3. **既存ドキュメント削除**: 同じ `source_path` を持つドキュメントがあれば削除（重複防止）
4. **ファイルダウンロード**: GCSからローカルの一時ファイルにダウンロード
5. **File Search Storeに登録**: メタデータを付与してGemini File Search APIに登録

```python
class EventHandler:
    def _handle_event_by_type(self, event_type, bucket_name, file_name):
        if event_type == "google.cloud.storage.object.v1.finalized":
            self._handle_file_upload(bucket_name, file_name)
        elif event_type == "google.cloud.storage.object.v1.deleted":
            self._handle_file_delete(bucket_name, file_name)
```

### ファイル削除時の処理フロー

ファイルがGCSから削除されると、対応するドキュメントをGemini File Search APIからも削除します。

1. **イベント受信・パスフィルタリング**
2. **メタデータでFile Search Store内を検索**: `source_path` メタデータで一致するドキュメントを特定
3. **ドキュメント削除**: File Search Storeから該当ドキュメントを削除

## Gemini File Search APIとの統合

### GCPエコシステムでの統一

Lawveではインフラ全体をGCPで統一する方針を取りました。RAGサービスとしてOpenAIやPineconeなど他の選択肢もありますが、Cloud Run、GCS、EventarcとシームレスにつながるGemini File Search APIを採用しています。

### File Search Storeの概要

Gemini File Search Storeは、ドキュメントを登録するとベクトル化・インデックス化を行い、セマンティック検索を提供するマネージドサービスです。バックエンドでは以下の操作を行います。

- **ドキュメント登録**: ファイルとメタデータをアップロード
- **ドキュメント検索**: メタデータをキーに既存ドキュメントを検索
- **ドキュメント削除**: 不要になったドキュメントを削除

### メタデータを活用した管理

各ドキュメントには以下の3つのメタデータを付与し、管理に活用しています。

| メタデータ | 用途 | 例 |
|---|---|---|
| `law_id` | 法令ID。法令の一意識別に使用 | `323AC0000000205` |
| `source_type` | ドキュメントの分類 | `user`, `doc`, `admin` |
| `source_path` | GCSの完全パス。ドキュメントの一意識別に使用 | `gs://bucket/file-search/archive/user/323AC0000000205/医療法.txt` |

`source_path` はGCSパスそのものであるため、GCSのファイルとFile Search Store内のドキュメントを一意に対応付けることができます。

```python
metadata = {
    "law_id": law_id,
    "source_path": source_path,
    "source_type": source_type
}

custom_metadata = [
    {"key": key, "string_value": value}
    for key, value in metadata.items()
]
```

## 汎用性を持たせた設計の工夫

### パスベースのメタデータ抽出

バックエンドは法令データに限定せず、GCSの特定パスに配置されたあらゆるファイルを処理できるよう設計しています。パスの規約は以下の通りです。

```
gs://<bucket>/file-search/archive/<source_type>/<law_id>/<file_name>
```

パスから `source_type` と `law_id` を正規表現で自動抽出します。

```python
# パスパターン
GCS_PATH_PATTERN = r'file-search/archive/([^/]+)/([^/]+)/.+'

def parse_gcs_path(gcs_path: str) -> Tuple[Optional[str], Optional[str]]:
    match = re.search(GCS_PATH_PATTERN, gcs_path)
    if match:
        return match.group(1), match.group(2)
    return None, None
```

この設計により、法令データだけでなく社内文書や技術ドキュメントなど、任意のドキュメントを同じ仕組みで管理できます。`source_type` を変えるだけで分類を追加でき、パス規約さえ守ればバックエンドのコード変更は不要です。

### GCSとGemini File Search APIの状態同期の保証

GCSを Single Source of Truth（信頼できる唯一の情報源）とし、File Search Storeの状態を常にGCSと一致させることを設計原則としています。

**アップロード時の冪等性**: 同じファイルを再度アップロードした場合、既存のドキュメントを削除してから再登録します。これにより重複を防ぎつつ、内容の更新にも対応できます。

```python
def process_file_upload(self, bucket_name, file_path):
    source_path = f"gs://{bucket_name}/{file_path}"

    # 既存ドキュメントの確認・削除
    self._delete_existing_documents(source_path)

    # 新規アップロード
    law_id = extract_law_id(file_path)
    local_file_path = self.gcs_client.download_to_temp_file(bucket_name, file_path)
    # ...
    success = self.store_client.upload_file(local_file_path, file_path, metadata)
    return success
```

**削除時の連動**: GCSからファイルが削除されると、File Search Storeからも対応するドキュメントが自動的に削除されます。`source_path` メタデータをキーに検索し、一致するドキュメントを削除します。

```python
def process_file_delete(self, bucket_name, file_path):
    source_path = f"gs://{bucket_name}/{file_path}"

    documents = self.store_client.find_documents_by_source_path(source_path)
    if not documents:
        return False

    return self.store_client.delete_document(documents[0])
```

### ローカルダウンロード経由の設計

GCSからGemini File Search APIへの登録は、一度ローカルの一時ファイルにダウンロードしてから行う設計にしています。

```python
def download_to_temp_file(self, bucket_name, file_path):
    _, ext = os.path.splitext(file_path)
    if not ext:
        ext = ".txt"

    temp_fd, temp_path = tempfile.mkstemp(suffix=ext)
    os.close(temp_fd)

    blob.download_to_filename(temp_path)
    return temp_path
```

直接アップロードする方がシンプルですが、ローカルに一度保存することで以下のメリットがあります。

- **拡張性**: 将来ExcelをCSVに変換するなど、アップロード前のファイル加工処理を挟める
- **MIME判定**: ファイル拡張子を保持することで正確なMIMEタイプ判定が可能
- **デバッグ**: 問題発生時にローカルファイルの内容を確認できる

## エラーハンドリングと運用設計

### 常に200 OKを返す設計

Cloud RunのエンドポイントはEventarcからのリクエストに対し、エラーが発生しても常に200 OKを返します。

```python
@app.post("/")
async def handle_storage_event(request: Request):
    try:
        headers = dict(request.headers)
        body = await request.body()
        event = from_http(headers, body)
        result = event_handler.process(event)
        return result
    except Exception as e:
        return {
            "status": "error",
            "message": f"Event processing failed: {str(e)}",
            "note": "Error logged and notified via Slack"
        }
```

これはEventarcのリトライを防ぐための設計です。エラー時に4xx/5xxを返すとEventarcがイベントを再送し、同じエラーが繰り返し発生します。結果としてSlack通知が重複し、ログが汚染されます。エラーはSlack通知とログで把握し、200 OKで受け取り済みとすることで運用上の問題を防いでいます。

### Slack通知によるエラー監視

File Search Storeへのアップロード失敗時にはSlack Webhookでエラー通知を送信します。通知にはGCSパスとエラー詳細を含め、運用者が問題箇所を素早く特定できるようにしています。

このSlack通知による監視はハッカソンの開発規模では十分ですが、実運用ではCloud Monitoringによるアラート設定やCloud Loggingとの連携、Dead Letter Queueによる失敗イベントの再処理など、より堅牢な監視・リカバリの仕組みが必要になるでしょう。

### 遅延初期化によるクライアント管理

Gemini APIクライアントやGCSクライアントは遅延初期化（Lazy Initialization）を採用しています。

```python
@property
def client(self) -> Optional[genai.Client]:
    if self._client is None and self.api_key:
        self._client = genai.Client(api_key=self.api_key)
    return self._client
```

APIキーが未設定の場合でもアプリケーションが起動でき、テスト環境やローカル開発時にすべての環境変数を揃えなくても動作します。

## まとめ

Lawveのバックエンドは、以下の設計原則に基づいて構築しました。

- **イベント駆動による疎結合な自動同期**: Eventarcを活用し、フロントエンドとバックエンドを疎結合に保ちつつGCSの変更を自動的にGemini File Search APIに反映
- **メタデータ駆動の汎用設計**: パス規約からメタデータを自動抽出し、法令以外のドキュメントにも対応可能な拡張性を確保
- **GCSをSingle Source of Truthとした状態管理**: 冪等なアップロードと連動削除により、GCSとFile Search Storeの状態を常に一致させる

イベント駆動アーキテクチャとGCPのマネージドサービスを組み合わせることで、少ないコードで信頼性の高いドキュメント同期基盤を実現できました。GCSにファイルを配置するだけで検索可能になるシンプルさは、ハッカソンの限られた時間内での開発にも大きく貢献しました。

ソースコードは[GitHub](https://github.com/23-u-don/lawve-backend)で公開しています。

## 関連記事

- [MacでUVを用いてPythonの開発環境を構築する](/blog/032-python-uv/)（Python環境構築）
- [OpenAI Response APIの使い方まとめ](/blog/044-openai-response-api/)（OpenAI APIの利用）
