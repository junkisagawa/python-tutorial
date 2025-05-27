# HTTPリクエスト

HTTPリクエストとは、Webサーバーに対してデータの送信や取得を行うための仕組みです。代表的なリクエストの種類は以下の通りです：

* GET：データの取得（例：天気情報を見る）
* POST：データの送信（例：フォームの送信）
* PUT：データの更新
* DELETE：データの削除

Pythonでは、requestsライブラリを使うことで、これらの操作を簡単に行えます。

---

# Web APIへリクエスト

Pythonの標準ライブラリには含まれていないので、最初に requests をインストールする必要があります。  
```bash  
pip install requests
```  

以下のコードは、ダミーのAPI（JSONPlaceholder）に GET リクエストを送り、ユーザー情報を取得する例です。  
```python  
import requests

# APIのURL
url = "https://jsonplaceholder.typicode.com/users/1"

# GETリクエストを送信
response = requests.get(url)

# 結果をJSON形式で取得
user_data = response.json()

# 結果を表示
print("ユーザー名:", user_data["name"])
print("メールアドレス:", user_data["email"])
```   

# 実行結果  
以下は、上記コードを実行したときの出力例です。  
```  
ユーザー名: Leanne Graham
メールアドレス: Sincere@april.biz
```  
このように、Pythonの requests ライブラリを使えば、Web APIと簡単に連携することができます。  

---

> requestsを使うことで、HTTPリクエストを送ることができて、外部サービスと連携することができます！

[◀ 戻る：エラー処理](python_basic_error.md)