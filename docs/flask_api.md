# Flask API開発

---

## API設計のベストプラクティス
- エンドポイントは「名詞」で設計（例：/users, /tasks）
- HTTPメソッド（GET/POST/PUT/DELETE）を正しく使う
- ステータスコード・エラーメッセージを明確に返す
- 入力値バリデーションを必ず行う
- ドキュメント（OpenAPI/Swagger等）を用意

---

## サンプル：ユーザー管理API設計例

| メソッド | パス         | 内容             |
|----------|--------------|------------------|
| GET      | /users       | ユーザー一覧取得 |
| POST     | /users       | ユーザー新規作成 |
| GET      | /users/<id>  | ユーザー詳細取得 |
| PUT      | /users/<id>  | ユーザー更新     |
| DELETE   | /users/<id>  | ユーザー削除     |

---

## FlaskでのAPI実装例

```python
from flask import Flask, jsonify, request, abort
app = Flask(__name__)

users = [
    {"id": 1, "name": "Alice"},
    {"id": 2, "name": "Bob"}
]

@app.route('/users', methods=['GET'])
def get_users():
    return jsonify(users)

@app.route('/users/<int:user_id>', methods=['GET'])
def get_user(user_id):
    user = next((u for u in users if u["id"] == user_id), None)
    if user is None:
        abort(404, description="User not found")
    return jsonify(user)

@app.route('/users', methods=['POST'])
def create_user():
    if not request.json or not "name" in request.json:
        abort(400, description="Name is required")
    new_id = max(u["id"] for u in users) + 1
    user = {"id": new_id, "name": request.json["name"]}
    users.append(user)
    return jsonify(user), 201

# PUT/DELETEも同様に実装可能

if __name__ == '__main__':
    app.run(debug=True)
```

---

> APIは「誰が使っても分かりやすい設計」を心がけましょう。

## Flask基本APIの作成（ハンズオン）

### 環境準備:

```bash
# Flaskをインストール
pip install flask

# プロジェクトディレクトリを作成
mkdir flask-api
cd flask-api
```

### 最小限のFlaskアプリ:

```python
# app.py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
    return 'Hello, Flask!'

if __name__ == '__main__':
    app.run(debug=True)
```

### JSONを返すAPIの例:

```python
# app.py
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/hello', methods=['GET'])
def hello_world():
    return jsonify({'message': 'Hello, World!'})

@app.route('/users/<username>', methods=['GET'])
def get_user(username):
    # 実際はデータベースから取得するケースが多い
    return jsonify({
        'username': username,
        'email': f'{username}@example.com'
    })

if __name__ == '__main__':
    app.run(debug=True)
```

## リクエストデータの処理

```python
# app.py
from flask import Flask, jsonify, request

app = Flask(__name__)

@app.route('/echo', methods=['POST'])
def echo():
    # JSONリクエストボディを取得
    data = request.json
    return jsonify(data)

@app.route('/search', methods=['GET'])
def search():
    # クエリパラメータを取得
    query = request.args.get('q', '')
    return jsonify({
        'query': query,
        'results': [f'Result for {query} - 1', f'Result for {query} - 2']
    })

if __name__ == '__main__':
    app.run(debug=True)
```

## APIの実行とテスト

Flaskアプリケーションを起動します。

```bash
python app.py
```

ブラウザまたはAPIクライアント（curl, Postman, HTTPieなど）で以下のURLにアクセスして結果を確認してください。

* `http://127.0.0.1:5000/hello` - Hello World JSONレスポンスを取得
* `http://127.0.0.1:5000/users/alice` - ユーザー情報を取得
* `http://127.0.0.1:5000/search?q=python` - 検索クエリを送信

POSTリクエストの送信例（curl使用）:

```bash
curl -X POST -H "Content-Type: application/json" -d '{"name":"John","age":30}' http://127.0.0.1:5000/echo
``` 