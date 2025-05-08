# エラーハンドリングとデバッグ

---

## よくあるエラーとその対処法

- **SyntaxError（構文エラー）**：スペルやカッコの閉じ忘れを確認
- **IndentationError（インデントエラー）**：スペースとタブの混在に注意
- **TypeError/ValueError**：型や値のミス。エラーメッセージをよく読む

---

## Flaskでのエラーハンドリング例

```python
from flask import Flask, jsonify, request
app = Flask(__name__)

@app.errorhandler(404)
def not_found(error):
    return jsonify({'error': 'Not found'}), 404

@app.errorhandler(400)
def bad_request(error):
    return jsonify({'error': 'Bad request'}), 400

@app.route('/divide')
def divide():
    try:
        a = int(request.args.get('a', 0))
        b = int(request.args.get('b', 0))
        if b == 0:
            return jsonify({'error': 'Division by zero'}), 400
        return jsonify({'result': a / b})
    except Exception as e:
        return jsonify({'error': str(e)}), 400
```

---

## デバッグのコツ
- エラー文をよく読む（どの行で何が起きたか）
- print()で値を確認
- Flaskの`debug=True`で詳細なエラー画面
- ログ出力（loggingモジュール）も活用

```python
import logging
logging.basicConfig(level=logging.INFO)
logging.info("ここまで実行できた！")
```

---

> エラーは「成長のチャンス」！焦らず一つずつ解決しましょう。 