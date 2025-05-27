# FastAPI入門

## FastAPIとは？

FastAPIは、PythonでモダンなWeb APIを簡単に・高速に作るためのフレームワークです。
少ないコードでWebサービスやアプリのAPIを作ることができます。

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello FastAPI!"}
```

---

## FastAPIの特徴

| 特徴                 | 説明                              |
| ------------------ | ------------------------------- |
| ✅ 高速               | Pythonの中でもトップクラスのAPI処理速度（非同期対応） |
| ✅ 自動ドキュメント生成       | ブラウザでAPIの使い方が確認できる（Swagger UI）  |
| ✅ 型ヒントで自動チェック      | 入力データを自動でバリデーション（Pydanticと連携）   |
| ✅ シンプルなコード         | 少ないコードでAPIが作れる                  |
| ✅ Python 3.7以降向け設計 | モダンなPythonの書き方（型ヒント・async）に対応   |

---

## どんなシーンで使われるか

FastAPIは以下のような場面で活躍します：

* 📱 **Webアプリのバックエンド**
  アプリとやり取りするAPIとして使われます。

* 🔁 **マイクロサービス間の通信**
  サービスを分割してAPIでつなぐシステムでFastAPIは軽量・高速で相性◎

* 💻 **業務システムや社内ツールのAPI**
  業務データの取得・登録をWeb経由で行うAPIの開発に向いています。
