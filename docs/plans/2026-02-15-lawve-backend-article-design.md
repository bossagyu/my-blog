# 記事設計: GCSとGemini File Search APIを自動同期するイベント駆動バックエンドの設計

## メタ情報

- **記事番号:** 048
- **ディレクトリ:** `content/blog/048-lawve-backend/`
- **カテゴリ:** Engineering
- **タグ:** Python, GCP, Gemini, FastAPI
- **対象読者:** エンジニア（GCPやイベント駆動アーキテクチャに興味がある人）
- **アプローチ:** アーキテクチャ解説型

## 背景

- デジタル庁「法令×デジタル」ハッカソン第三弾で開発したプロダクト「Lawve」のバックエンド
- 4-5人チームで参加、筆者はバックエンド開発・インフラ構築・アーキテクチャ設計を担当
- 法令検索の不便さと業務効率化を動機として開発
- GCPで統一する方針のためGemini File Search APIを選定
- ハッカソン体験記は別記事で作成予定のため、本記事は技術に集中

## フロントマター

### 日本語 (index.md)

```toml
+++
title = "GCSとGemini File Search APIを自動同期するイベント駆動バックエンドの設計"
description = "Google Cloud StorageのファイルをEventarc経由でGemini File Search APIと自動同期するバックエンドをFastAPIで構築。イベント駆動アーキテクチャの設計思想と汎用性を持たせた実装のポイントを解説します。"
date = 2026-02-15T00:00:00+09:00
draft = false
categories = ["Engineering"]
tags = ["Python", "GCP", "Gemini", "FastAPI"]
+++
```

### 英語 (index.en.md)

```toml
+++
title = "Designing an Event-Driven Backend to Sync GCS with Gemini File Search API"
description = "Building a FastAPI backend that automatically syncs Google Cloud Storage files with Gemini File Search API via Eventarc. Covers event-driven architecture design and implementation patterns for extensibility."
date = 2026-02-15T00:00:00+09:00
draft = false
categories = ["Engineering"]
tags = ["Python", "GCP", "Gemini", "FastAPI"]
+++
```

## 記事構成

### 1. 概要

- デジタル庁「法令×デジタル」ハッカソンで開発したLawveの紹介（簡潔に）
- この記事ではバックエンドのアーキテクチャ設計に焦点を当てることを明示
- 解決したい課題：法令検索の不便さ、複数ソースの横断検索

### 2. システム全体像

- Mermaidによるアーキテクチャ図
  - GCS → Eventarc → Cloud Run（FastAPI） → Gemini File Search API
  - フロントエンド（Next.js / Cloud Run）→ Gemini File Search API / e-Gov API / Firestore
- 各コンポーネントの役割を簡潔に説明
- バックエンドの責務を明確化：「GCSとGemini File Search APIの状態同期」

### 3. イベント駆動アーキテクチャの設計

- なぜイベント駆動を選んだか
  - GCSの変更をトリガーにした自動同期が必要
  - フロントエンドとバックエンドの疎結合化
- Eventarcによるイベントルーティングの仕組み
  - `google.cloud.storage.object.v1.finalized` (アップロード)
  - `google.cloud.storage.object.v1.deleted` (削除)
- CloudEventsフォーマットでの受信処理
- ファイルアップロード時の処理フロー（図またはリスト）
  1. イベント受信 → パスフィルタリング
  2. メタデータ抽出（law_id, source_type）
  3. 既存ドキュメント削除（重複防止）
  4. GCSからダウンロード → File Search Storeに登録
- ファイル削除時の処理フロー
  1. イベント受信 → パスフィルタリング
  2. メタデータでFile Search Store内を検索
  3. 該当ドキュメントを削除

### 4. Gemini File Search APIとの統合

- GCPエコシステムで統一するためにGeminiを選定した理由
- File Search Storeの概要（ドキュメントの登録・検索・削除）
- メタデータ（law_id, source_type, source_path）を活用した管理
  - source_pathによるドキュメントの一意識別
  - メタデータフィルタによる検索

### 5. 汎用性を持たせた設計の工夫

- **パスベースのメタデータ抽出**
  - `gs://bucket/file-search/archive/{source_type}/{law_id}/{file_name}` のパス規約
  - パスからメタデータを自動抽出する仕組み
  - 法令以外のドキュメントにも対応可能
- **GCSとGemini File Search APIの状態同期の保証**
  - アップロード時：既存ドキュメントを削除してから再登録（冪等性）
  - 削除時：GCSの削除に連動してFile Search Storeからも削除
  - 常にGCSが正（Single Source of Truth）
- **ローカルダウンロード経由の設計**
  - 一度ローカルにダウンロードしてからアップロードする構成
  - 将来のファイル加工処理（Excel→CSV変換等）に対応可能な拡張ポイント

### 6. エラーハンドリングと運用設計

- 常に200 OKを返す設計の理由
  - Eventarcのリトライを防止し、重複処理やSlack通知の重複を回避
- Slack Webhookによるエラー通知
  - エラー発生時にGCSパスやスタックトレースを含むメッセージを送信
- 遅延初期化（Lazy Initialization）によるクライアント管理
  - APIキー未設定時でもアプリが起動可能

### 7. まとめ

- アーキテクチャの要点の振り返り
  - イベント駆動による疎結合な自動同期
  - メタデータ駆動の汎用設計
  - GCSをSingle Source of Truthとした状態管理
- イベント駆動 + マネージドサービスの組み合わせの利点

## 参考ソースコード

記事内でコードを引用する主要ファイル:

- `app/main.py` — FastAPIエンドポイント、CloudEvents受信
- `app/services/event_handler.py` — イベント処理のオーケストレーション
- `app/services/file_search_service.py` — GCS ↔ File Search Store同期ロジック
- `app/utils/path_extraction.py` — パスベースのメタデータ抽出
- `app/config.py` — 設定管理

## 除外事項

- SDK未対応のforce deleteをREST APIで回避した実装詳細
- 全角スペースの正規化など日本語文書固有の前処理
- ハッカソンの参加体験記（別記事で作成予定）
- フロントエンドの詳細実装
