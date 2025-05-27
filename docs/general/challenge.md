# 追加課題


## 作成中です



## 実践的なAPIサーバー構築

以下の機能を持つAPIサーバーを作成してください。

1. **四則演算APIの作成**:
   - `/calc`エンドポイントで、四則演算（足し算、引き算、掛け算、割り算）を行うAPIを作成
   - クエリパラメータまたはJSONリクエストで2つの数値と演算子を受け取る
   - 結果をJSON形式で返す
   - エラーハンドリング（数値以外の入力、ゼロ除算など）を実装

2. **タスク管理API**:
   - タスクの作成、取得、更新、削除ができるRESTful API
   - データはSQLiteまたはSQLAlchemyで保存
   - 以下のエンドポイントを実装
     - `GET /tasks`: タスク一覧取得
     - `GET /tasks/<id>`: 指定IDのタスク取得
     - `POST /tasks`: 新規タスク作成
     - `PUT /tasks/<id>`: タスク更新
     - `DELETE /tasks/<id>`: タスク削除

3. **ユーザー認証機能**:
   - シンプルなAPIキー認証の実装
   - 認証されていないリクエストは拒否する
   - ユーザーごとに異なるタスクリストを表示

---

## 発展的な課題

- RESTful API設計のベストプラクティスに従ってAPIを再設計
- スキーマバリデーションの追加（marshmallow等）
- **APIドキュメントの自動生成**（Swagger/OpenAPI, flasgger, apispec等）
- **テスト自動化**（pytest, unittest, coverage）
- **Dockerコンテナ化とデプロイ**（Dockerfile, docker-compose）
- CI/CDパイプラインの構築（GitHub Actions等）

---

> 「現場で本当に使えるAPI」を目指して、ぜひ挑戦してみてください！ 