# Pythonとは？

## なぜ今Pythonなのか

Pythonは「シンプルで読みやすい」「学習コストが低い」「現場での活用範囲が広い」ことから、世界中の企業やエンジニアに選ばれています。

* 2020年代のプログラミング言語人気ランキングで常に上位
* Web開発、データ分析、AI、業務自動化など多様な分野で活躍
* 日本国内でも金融・製造・官公庁など幅広い現場で導入実績あり

## Pythonの歴史と進化

* 1991年、オランダのグイド・ヴァンロッサム氏が開発
* 「誰が読んでも分かりやすいコード」を目指して設計
* 2系→3系への大きな進化（現在は3系が主流）
* オープンソースで世界中の開発者が改良に参加

## 他言語との比較

| 言語         | 特徴・用途例          | Pythonとの違い  |
| ---------- | --------------- | ----------- |
| COBOL      | 基幹業務・バッチ処理      | レガシー資産が多い   |
| Java       | 大規模Web/業務システム   | 静的型付け・冗長な記述 |
| JavaScript | フロントエンド/Node.js | Webブラウザで動作  |
| Python     | Web/AI/自動化/教育など | シンプル・多用途    |

## Pythonの哲学（The Zen of Python）

Pythonには「美しいコードを書くための哲学」があります。

以下のコードを実行すると、その哲学を見ることができます。

```bash
python -c "import this"
```

実行方法：

1. ターミナル（コマンドプロンプト）を開く
2. 上記のコマンドを入力し、Enterキーを押す

> Beautiful is better than ugly.（美は醜さに勝る）
> Simple is better than complex.（シンプルは複雑に勝る）

現場でも大切にされる考え方が詰まっています。

## Pythonの主な特徴

* **インデントで構造を表現**：可読性が高い
* **動的型付け**：柔軟で素早い開発が可能
* **豊富な標準/外部ライブラリ**：API開発、データ処理、AIなど何でもできる
* **マルチパラダイム**：手続き型・オブジェクト指向・関数型もOK
* **クロスプラットフォーム**：Windows/Mac/Linuxで同じコードが動く

## Pythonの実行環境構築（pyenvを使った仮想環境）

Pythonのバージョン管理と仮想環境構築にはpyenvを利用します。

### pyenvのインストール方法

macOSの場合（Homebrew使用）:

```bash
brew install pyenv
```

Windowsの場合（scoop使用）:

```powershell
scoop install pyenv-win
```

### Pythonのバージョン指定・インストール

```bash
pyenv install 3.11.0
pyenv global 3.11.0
```

### 仮想環境の作成と有効化

```bash
# 仮想環境を作成（プロジェクトごとに推奨）
python -m venv myenv

# 仮想環境を有効化（macOS/Linuxの場合）
source myenv/bin/activate

# 仮想環境を有効化（Windowsの場合）
.\myenv\Scripts\activate
```

仮想環境から抜けるには`deactivate`コマンドを実行します。

## Pythonスクリプトの実行方法

以下の手順でPythonスクリプトを作成・実行します。

### 1. Pythonスクリプト作成

`hello.py`というファイルを作成し、次のコードを記述します。

```python
print("Hello, Python!")
```

### 2. ターミナルでスクリプト実行

ターミナルを開き、以下を入力して実行します。

```bash
python hello.py
```

出力結果:

```
Hello, Python!
```

## 現場でのPython活用例

* Webアプリ/APIサーバー（Flask, Django）
* データ分析・機械学習（pandas, scikit-learn, TensorFlow）
* 業務自動化（Excel操作、ファイル変換、RPA）
* IoT/組み込み開発、教育現場

## Pythonコミュニティ・情報源

* 公式ドキュメント（日本語あり）: [https://docs.python.org/ja/3/](https://docs.python.org/ja/3/)
* PyCon JP（日本最大のPythonカンファレンス）
* Qiita、Zenn、teratailなど日本語情報も豊富

---
[環境セットアップ](python_env.md)