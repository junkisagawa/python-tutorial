# FastAPIを使ったAPI開発

---

## FastAPIのインストール

FastAPIと開発用サーバー（`uvicorn`）をインストールします。  
参考リンク: [https://fastapi.tiangolo.com/ja/#_3](https://fastapi.tiangolo.com/ja/#_3)

```bash
pip install fastapi uvicorn
```

---

## 簡単な実装例・実行

### ファイル: `main.py`

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello FastAPI!"}
```

### 実行コマンド:

```bash
uvicorn main:app --reload
```

ブラウザまたはAPIクライアント（curl, Postman, HTTPieなど）で以下のURLにアクセスして結果を確認できます。

---

## GETメソッドの実装例

```python
@app.get("/hello")
def say_hello():
    return {"message": "Hello, GET!"}
```

---

## POSTメソッドの実装例

```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    price: float

@app.post("/items")
def create_item(item: Item):
    return {"message": f"{item.name}（{item.price}円）を登録しました"}
```

---

## PUTメソッドの実装例

```python
@app.put("/items/{item_id}")
def update_item(item_id: int, item: Item):
    return {"message": f"ID {item_id} の商品を更新しました", "item": item}
```

---

## DELETEメソッドの実装例

```python
@app.delete("/items/{item_id}")
def delete_item(item_id: int):
    return {"message": f"ID {item_id} の商品を削除しました"}
```

---

## パスパラメーターの受け取りかた

```python
@app.get("/users/{user_id}")
def get_user(user_id: int):
    return {"user_id": user_id}
```

> `/users/123` にアクセスすると `{"user_id": 123}` が返ります。

---

## リクエストボディの受け取りかた

```python
class User(BaseModel):
    name: str
    age: int

@app.post("/users")
def create_user(user: User):
    return {"message": f"{user.name}（{user.age}歳）を登録しました"}
```