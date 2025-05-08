# Flask入門

---

## Flaskとは？

Flaskは「シンプルで拡張性が高い」Python製のWebフレームワークです。

- 必要最小限の機能だけを持ち、自由にカスタマイズできる
- 小規模なAPIから本格的なWebサービスまで幅広く対応

---

## FlaskとDjangoの違い

| フレームワーク | 特徴 | 向いている用途 |
|---|---|---|
| Flask | 軽量・自由度高い | API、プロトタイプ、小規模Web |
| Django | 機能豊富・規約重視 | 大規模Web、管理画面、認証付きサイト |

- Flaskは「自分で組み立てたい人」向け、Djangoは「全部入りで楽したい人」向け

---

## Flaskのディレクトリ構成例

```
myapp/
├── app.py
├── requirements.txt
├── static/
├── templates/
└── ...
```
- `app.py`：アプリ本体
- `static/`：CSSや画像など静的ファイル
- `templates/`：HTMLテンプレート

---

## 実務でよく使うFlask拡張

- **Flask-RESTful**：API開発を楽にする
- **Flask-Login**：ログイン認証を簡単に
- **Flask-Migrate**：DBマイグレーション管理
- **Flask-CORS**：他ドメインからのAPIアクセス許可

インストール例：
```bash
pip install flask-restful flask-login flask-migrate flask-cors
```

---

## 公式ドキュメント・学習リソース
- [Flask公式](https://flask.palletsprojects.com/ja/latest/)
- [Flask-RESTful公式](https://flask-restful.readthedocs.io/ja/latest/)
- [Flask-Login公式](https://flask-login.readthedocs.io/en/latest/)

---

> Flaskは「最初はシンプル、必要に応じてパワフル」なフレームワークです！ 