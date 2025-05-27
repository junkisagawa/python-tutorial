# DB連携（SQLite & SQLAlchemy）

---

## SQLite（標準ライブラリ）での連携例

```python
import sqlite3
conn = sqlite3.connect('sample.db')
cursor = conn.cursor()
cursor.execute('CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, name TEXT)')
cursor.execute('INSERT INTO users (name) VALUES (?)', ("山田",))
conn.commit()
for row in cursor.execute('SELECT * FROM users'):
    print(row)
conn.close()
```

---

## ORM（SQLAlchemy）での連携例

SQLAlchemyは「PythonのコードだけでDB操作ができる」便利なライブラリです。

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///sample.db'
db = SQLAlchemy(app)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(50))

with app.app_context():
    db.create_all()
    user = User(name="佐藤")
    db.session.add(user)
    db.session.commit()
    users = User.query.all()
    for u in users:
        print(u.id, u.name)
```

---

> ORMを使うと「SQL文を書かずに」DB操作ができ、保守性・安全性もアップします。

```python
# app.py
import sqlite3
from flask import Flask, jsonify, request, g

app = Flask(__name__)

# データベース接続を取得
def get_db():
    db = getattr(g, '_database', None)
    if db is None:
        db = g._database = sqlite3.connect('tasks.db')
        db.row_factory = sqlite3.Row
    return db

# アプリケーション終了時にDBをクローズ
@app.teardown_appcontext
def close_connection(exception):
    db = getattr(g, '_database', None)
    if db is not None:
        db.close()

# 初期化
def init_db():
    with app.app_context():
        db = get_db()
        with app.open_resource('schema.sql', mode='r') as f:
            db.cursor().executescript(f.read())
        db.commit()

# タスクリスト取得API
@app.route('/tasks', methods=['GET'])
def get_tasks():
    db = get_db()
    cursor = db.execute('SELECT id, title, done FROM tasks')
    tasks = [dict(id=row['id'], title=row['title'], done=row['done']) for row in cursor.fetchall()]
    return jsonify(tasks)

# タスク追加API
@app.route('/tasks', methods=['POST'])
def add_task():
    if not request.json or not 'title' in request.json:
        return jsonify({'error': 'Title is required'}), 400
    
    title = request.json['title']
    db = get_db()
    cursor = db.execute('INSERT INTO tasks (title, done) VALUES (?, ?)', 
                        [title, False])
    db.commit()
    
    return jsonify({
        'id': cursor.lastrowid,
        'title': title,
        'done': False
    }), 201

if __name__ == '__main__':
    app.run(debug=True)
```

schema.sqlファイル:

```sql
DROP TABLE IF EXISTS tasks;
CREATE TABLE tasks (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  title TEXT NOT NULL,
  done BOOLEAN NOT NULL DEFAULT FALSE
);
``` 