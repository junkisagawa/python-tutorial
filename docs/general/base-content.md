# Python & Flask トレーニング

## 第1章: はじめに

### 1.1 トレーニングの目的

本トレーニングの目的は、COBOLなどの経験を持つエンジニアが、Pythonの基本的な文法やテクニックを習得し、Flaskを使ったAPI開発を実践できるようになることです。3時間のセッションを通じて、以下を学びます：

- Pythonの基礎知識と環境構築
- 基本的なプログラミング構造と文法
- FlaskフレームワークでのWeb API開発
- 実践的なハンズオン演習

### 1.2 Pythonの位置づけと特徴

Pythonは汎用性が高く、Web開発からデータ分析、機械学習、AIまで幅広く活用されているプログラミング言語です。特にWeb API開発においては、シンプルで強力なフレームワークであるFlaskやDjangoがよく使われています。

Pythonは近年のプログラミング言語人気ランキングで常に上位に位置し、多くのエンジニアに選ばれています。

### 1.3 学習の進め方

このトレーニングでは、講義とハンズオンを交互に行いながら進めていきます。各セクションの後に練習問題を解くことで理解を深めていきましょう。質問はいつでも歓迎します。

## 第2章: Pythonとは？

### 2.1 Pythonの哲学

Pythonは、読みやすさと生産性を重視した、シンプルで明確なプログラミング言語です。

```python
import this  # Pythonの哲学を表示
```

実行すると「The Zen of Python」が表示され、その中には「読みやすさが大事」「シンプルさが複雑さに勝る」といった考え方が含まれています。

### 2.2 Pythonの主な特徴

- **読みやすいシンタックス**: インデントによる構造定義
- **動的型付け**: 変数の型を事前に宣言する必要がない
- **インタープリタ言語**: コンパイル不要で実行可能
- **マルチパラダイム**: 手続き型、オブジェクト指向型、関数型など様々なスタイルに対応
- **豊富なライブラリ**: 標準ライブラリとサードパーティライブラリ

### 2.3 なぜPythonを使うのか？

- **読みやすく、保守性が高い**: 明快な構文で、他の開発者が読みやすいコードを書ける
- **生産性**: 少ないコード行数で多くの処理を実現可能
- **多用途**: Web開発、データ分析、AI、自動化など様々な分野で活用可能
- **コミュニティが活発**: 豊富なライブラリと情報源
- **企業での採用増加**: 多くの企業がPythonを採用し、需要が高まっている

### 2.4 Pythonの用途

- Webアプリケーション開発
- データ分析・可視化
- 機械学習・AI
- 自動化スクリプト
- ゲーム開発
- IoTデバイスプログラミング

## 第3章: Python環境のセットアップ（ハンズオン）

### 3.1 Pythonのインストール

```bash
# Python 3.11をインストール
pyenv install 3.11.0

# インストール済みのPythonバージョンを確認
pyenv versions
```

### 3.2 仮想環境の作成と活用

仮想環境はプロジェクト毎に異なるバージョンのパッケージを管理するために重要です。

```bash
# 仮想環境を作成
pyenv virtualenv 3.11.0 flask-training

# 仮想環境を有効化
pyenv activate flask-training

# 現在の環境を確認
python --version
pip --version
```

### 3.3 基本的なパッケージ管理

```bash
# pipの更新
pip install --upgrade pip

# パッケージのインストール
pip install requests

# インストール済みパッケージの確認
pip list

# 必要なパッケージをファイルに出力
pip freeze > requirements.txt

# requirementsファイルからインストール
pip install -r requirements.txt
```

## 第4章: Python基本文法

### 4.1 変数と型

Pythonは動的型付け言語で、変数の型を自動で判断します。

```python
# 基本的な型
num = 10        # 整数 (int)
pi = 3.14       # 浮動小数点数 (float)
text = "Hello"  # 文字列 (str)
is_active = True  # 真偽値 (bool)
nothing = None  # None型

# 型の確認
print(type(num))      # <class 'int'>
print(type(pi))       # <class 'float'>
print(type(text))     # <class 'str'>
print(type(is_active))  # <class 'bool'>
```

**型変換（キャスト）**

```python
# 文字列から数値へ
num_str = "42"
num_int = int(num_str)
num_float = float(num_str)

# 数値から文字列へ
back_to_str = str(num_int)
```

**文字列操作**

```python
# 文字列連結
greeting = "Hello"
name = "Python"
message = greeting + ", " + name + "!"  # "Hello, Python!"

# f-strings（Python 3.6以降）
message = f"{greeting}, {name}!"  # "Hello, Python!"

# 文字列メソッド
print("python".upper())  # "PYTHON"
print("PYTHON".lower())  # "python"
print("  python  ".strip())  # "python"
print("python".replace("p", "j"))  # "jython"
```

**練習問題:**
* 名前と年齢を変数に入れて、自己紹介文を作成し表示するプログラムを作成してください。
   * 例：「私の名前は〇〇で、年齢は〇〇歳です。」
* 小数点を含む数値を入力として受け取り、整数部分と小数部分を別々に表示するプログラムを作成してください。

### 4.2 条件分岐

条件に応じて処理を変更します。

```python
age = 20
if age >= 18:
    print("成人")
else:
    print("未成年")

# 複数条件
score = 85
if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
else:
    grade = "D"
print(f"あなたの成績は{grade}です")

# 論理演算子
is_student = True
has_id = True
if is_student and has_id:
    print("学生割引が適用されます")

# 三項演算子
status = "成人" if age >= 18 else "未成年"
```

**練習問題:**
* ユーザーが入力した数値を受け取り、偶数か奇数かを判定し、結果を表示するプログラムを作成してください。
* 年齢と性別を入力として受け取り、以下の条件で出力するプログラムを作成してください。
  * 男性で20歳以上: "男性成人"
  * 女性で20歳以上: "女性成人"
  * 20歳未満: "未成年"

### 4.3 ループ（繰り返し）

処理を繰り返し行います。

```python
# for ループ
for i in range(5):  # 0, 1, 2, 3, 4
    print(i)

# リストでのループ
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(fruit)

# 辞書でのループ
person = {"name": "Alice", "age": 25}
for key in person:
    print(f"{key}: {person[key]}")

# key, valueのループ
for key, value in person.items():
    print(f"{key}: {value}")

# while ループ
count = 0
while count < 5:
    print(count)
    count += 1

# break と continue
for i in range(10):
    if i == 3:
        continue  # このイテレーションをスキップ
    if i == 7:
        break  # ループを抜ける
    print(i)
```

**練習問題:**
* 1から100までの数値のうち、偶数の合計を計算して表示するプログラムを作成してください。
* FizzBuzzプログラムを作成してください。1から30までの数値をループし、以下のルールで出力します。
  * 3の倍数: "Fizz"
  * 5の倍数: "Buzz"
  * 3と5の両方の倍数: "FizzBuzz"
  * それ以外: 数値そのもの

### 4.4 関数

再利用可能なコードのブロックです。

```python
# 基本的な関数
def greet(name):
    """挨拶を返す関数"""
    return f"Hello, {name}!"

print(greet("Alice"))  # "Hello, Alice!"

# デフォルト引数
def greet_with_time(name, time="morning"):
    return f"Good {time}, {name}!"

print(greet_with_time("Bob"))  # "Good morning, Bob!"
print(greet_with_time("Carol", "evening"))  # "Good evening, Carol!"

# 可変長引数
def sum_all(*numbers):
    total = 0
    for num in numbers:
        total += num
    return total

print(sum_all(1, 2, 3, 4, 5))  # 15

# キーワード引数
def build_profile(first_name, last_name, **user_info):
    profile = {
        'first_name': first_name,
        'last_name': last_name
    }
    profile.update(user_info)
    return profile

user = build_profile('Albert', 'Einstein',
                    location='Princeton',
                    field='Physics')
print(user)
```

**練習問題:**
* 複数の数値をリストで受け取り、最大値と最小値を返す関数を作成してください。
* 摂氏から華氏、および華氏から摂氏に変換する2つの関数を作成し、ユーザーの入力に応じて適切な変換を行うプログラムを作成してください。
  * 摂氏から華氏: F = C × 9/5 + 32
  * 華氏から摂氏: C = (F - 32) × 5/9

### 4.5 リストと辞書

複数のデータを効率的に管理します。

#### リスト

```python
# リストの作成
fruits = ["apple", "banana", "cherry"]

# 要素へのアクセス
print(fruits[0])  # "apple"

# リストの操作
fruits.append("orange")  # 追加
fruits.insert(1, "blueberry")  # 挿入
fruits.remove("banana")  # 削除
popped = fruits.pop()  # 最後の要素を取り出し
print(popped)  # "orange"

# リストのスライス
numbers = [0, 1, 2, 3, 4, 5]
print(numbers[1:4])  # [1, 2, 3]
print(numbers[:3])   # [0, 1, 2]
print(numbers[3:])   # [3, 4, 5]
print(numbers[::2])  # [0, 2, 4] - 2ステップごと

# リスト内包表記
squares = [x**2 for x in range(10)]
print(squares)  # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

even_squares = [x**2 for x in range(10) if x % 2 == 0]
print(even_squares)  # [0, 4, 16, 36, 64]
```

#### タプル（不変リスト）

```python
# タプルの作成
dimensions = (200, 50, 100)

# 要素へのアクセス
print(dimensions[0])  # 200

# タプルは変更不可
# dimensions[0] = 250  # エラー
```

#### 辞書

```python
# 辞書の作成
person = {"name": "Alice", "age": 25, "city": "Tokyo"}

# 要素へのアクセス
print(person["name"])  # "Alice"

# 辞書の操作
person["email"] = "alice@example.com"  # 追加
person["age"] = 26  # 更新
del person["city"]  # 削除

# キーの存在確認
if "name" in person:
    print("Name exists!")

# get メソッド（キーがない場合のデフォルト値を指定）
phone = person.get("phone", "未登録")
print(phone)  # "未登録"

# 辞書内包表記
squares_dict = {x: x**2 for x in range(5)}
print(squares_dict)  # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

#### セット（重複なしコレクション）

```python
# セットの作成
fruits_set = {"apple", "banana", "cherry", "apple"}
print(fruits_set)  # 重複は自動的に除去される

# セットの操作
fruits_set.add("orange")
fruits_set.remove("banana")

# セットの演算
set_a = {1, 2, 3, 4, 5}
set_b = {4, 5, 6, 7, 8}
print(set_a.union(set_b))        # 和集合: {1, 2, 3, 4, 5, 6, 7, 8}
print(set_a.intersection(set_b))  # 積集合: {4, 5}
print(set_a.difference(set_b))   # 差集合: {1, 2, 3}
```

**練習問題:**
* 辞書型を使い、自分のプロフィールを作成して、全ての項目を表示するプログラムを作成してください。
* 与えられた文字列内の単語の出現回数をカウントし、辞書として返す関数を作成してください。例えば "hello world hello" という入力に対して {"hello": 2, "world": 1} という辞書を返します。

### 4.6 エラー処理

```python
# 基本的な例外処理
try:
    number = int(input("数字を入力してください: "))
    result = 10 / number
    print(f"結果: {result}")
except ValueError:
    print("有効な数字を入力してください")
except ZeroDivisionError:
    print("0で割ることはできません")
except Exception as e:
    print(f"予期せぬエラー: {e}")
else:
    print("エラーなく処理が完了しました")
finally:
    print("処理を終了します")
```

**練習問題:**
* ユーザーに２つの数値を入力してもらい、割り算を行うプログラムを作成してください。ゼロ除算や数値以外の入力に対してエラー処理を実装してください。

### 4.7 ファイル操作

```python
# ファイルの書き込み
with open('sample.txt', 'w', encoding='utf-8') as file:
    file.write('Pythonでファイル操作\n')
    file.write('2行目のテキスト\n')

# ファイルの読み込み
with open('sample.txt', 'r', encoding='utf-8') as file:
    content = file.read()
    print(content)

# 1行ずつ読み込み
with open('sample.txt', 'r', encoding='utf-8') as file:
    for line in file:
        print(line.strip())  # 末尾の改行を除去
```

**練習問題:**
* ユーザーが入力したテキストをファイルに追記し、その後ファイルの全内容を表示するプログラムを作成してください。

## 第5章: FlaskによるAPI開発

### 5.1 Flaskとは？

Flaskは、Pythonの軽量なWebフレームワークです。「マイクロフレームワーク」と呼ばれ、最小限の機能を備えながら、必要に応じて拡張していくことができます。小規模なアプリケーションやAPI開発、プロトタイピングに最適です。

### 5.2 なぜFlaskを使うのか？

- **シンプルで習得が容易**: 少ないコードで動くWebアプリケーションを作成可能
- **柔軟性**: 最小限のコア機能に、必要な機能だけを追加可能
- **拡張性**: 多くのFlask拡張機能が利用可能
- **Pythonの強み**: データ処理やAIなどPythonの強みとの連携が容易

### 5.3 Flask基本APIの作成（ハンズオン）

#### 環境準備:

```bash
# Flaskをインストール
pip install flask

# プロジェクトディレクトリを作成
mkdir flask-api
cd flask-api
```

#### 最小限のFlaskアプリ:

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

#### JSONを返すAPIの例:

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

### 5.4 リクエストデータの処理

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

### 5.5 APIの実行とテスト

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

### 5.6 データベース連携（SQLite）

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

### 5.7 エラーハンドリング

```python
from flask import Flask, jsonify

app = Flask(__name__)

# カスタムエラーハンドリング
@app.errorhandler(404)
def not_found(error):
    return jsonify({'error': 'Not found'}), 404

@app.errorhandler(400)
def bad_request(error):
    return jsonify({'error': 'Bad request'}), 400

@app.errorhandler(500)
def server_error(error):
    return jsonify({'error': 'Internal server error'}), 500

@app.route('/divide', methods=['GET'])
def divide():
    try:
        a = int(request.args.get('a', 0))
        b = int(request.args.get('b', 0))
        if b == 0:
            return jsonify({'error': 'Division by zero'}), 400
        return jsonify({'result': a / b})
    except ValueError:
        return jsonify({'error': 'Invalid parameters'}), 400

if __name__ == '__main__':
    app.run(debug=True)
```

**練習問題:**
* `/about`という新しいエンドポイントを作成し、自分のプロフィールをJSON形式で返すAPIを作成してください。
* 四則演算（足し算、引き算、掛け算、割り算）を行うAPIエンドポイント`/calc`を作成し、クエリパラメータで演算子と数値を受け取って結果を返すようにしてください。

## 第6章: まとめと発展

### 6.1 学習のまとめ

このトレーニングでは、以下を学びました：

1. Pythonの基本と環境構築
2. 基本的なデータ型とその操作
3. 条件分岐、ループ、関数などの制御構造
4. リスト、辞書などのデータ構造
5. Flaskフレームワークの基礎
6. Web APIの作成方法
7. データベース連携の基本

### 6.2 次のステップ

今後のスキルアップのために以下の学習をお勧めします：

- **Python中級・上級トピック**:
  - オブジェクト指向プログラミング（クラス、継承等）
  - データ処理ライブラリ（pandas, numpy）
  - テスト駆動開発（pytest）
  - 非同期処理
  
- **Flaskの発展**:
  - Blueprint（モジュール化）
  - SQLAlchemyでのORM操作
  - Flask-RESTful拡張
  - 認証・認可の実装
  
- **Webアプリケーション開発**:
  - フロントエンド連携（JavaScript, React等）
  - デプロイメント（Docker、クラウド環境）
  - CI/CD

### 6.3 おすすめの参考資料

- 公式ドキュメント：[Python公式](https://docs.python.org/ja/3/)、[Flask公式](https://flask.palletsprojects.com/)
- オンラインコース：Udemy, Coursera, PluralSightなど
- 書籍：『Pythonクラッシュコース』『Flask Web開発』など

## 第7章: 追加課題（挑戦）

### 7.1 実践的なAPIサーバー構築

以下の機能を持つAPIサーバーを作成してください。

1. **四則演算APIの作成**:
   - `/calc`エンドポイントで、四則演算（足し算、引き算、掛け算、割り算）を行うAPIを作成
   - クエリパラメータまたはJSONリクエストで2つの数値と演算子を受け取る
   - 結果をJSON形式で返す
   - エラーハンドリング（数値以外の入力、ゼロ除算など）を実装

2. **タスク管理API**:
   - タスクの作成、取得、更新、削除ができるRESTful API
   - データはSQLiteに保存
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

### 7.2 発展的な課題

- RESTful API設計のベストプラクティスに従ってAPIを再設計
- スキーマバリデーションの追加（marshmallow等）
- APIドキュメントの自動生成（Swagger/OpenAPI）
- テスト自動化（pytest）の実装
- Dockerコンテナ化とデプロイ

## 付録

### A. 便利なツールとライブラリ

- **開発環境**: PyCharm, VS Code
- **パッケージ管理**: pip, pipenv, poetry
- **テスト**: pytest, unittest
- **Web開発**: Flask, Django
- **データ処理**: pandas, numpy
- **可視化**: matplotlib, seaborn
- **API開発**: Flask-RESTful, FastAPI

### B. よくあるエラーとデバッグ方法

- インデントエラー
- 名前エラー（未定義変数）
- 型エラー
- インポートエラー
- pdbを使ったデバッグ方法
- ロギングのベストプラクティス

### C. Pythonコーディング規約

- PEP 8: Pythonのコーディングスタイルガイド
- コメントと文書化
- 命名規則
- コードレイアウト
- リント（linting）ツール