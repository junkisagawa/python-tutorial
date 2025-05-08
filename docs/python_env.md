# Python環境のセットアップ（初心者向けガイド）

Pythonを使い始めるには「インストール」「エディタの準備」「仮想環境」の3つが大切です。  
事前準備でインストールまで完了している方は、[3. 仮想環境とは？](#3-仮想環境とは？)まで進んでください。

---

## 1. Pythonのインストール

### Windowsの場合

1. 公式サイト（[https://www.python.org/downloads/](https://www.python.org/downloads/)）から最新版をダウンロードします。
2. ダウンロードしたインストーラーを実行し、「Add Python to PATH」に必ずチェックを入れてインストールを開始します。
3. インストール後、コマンドプロンプトを開き、次のコマンドでPythonのバージョンを確認します。

```cmd
python --version
```

バージョン番号が表示されれば、正常にインストールされています。

### Macの場合

macOSには最初からPythonが入っていますが、最新版を利用するために「Homebrew」と「pyenv」の使用を推奨します。

#### Homebrewのインストール方法

ターミナルを開き、次のコマンドを入力します。

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

#### pyenvとPythonのインストール

```bash
brew install pyenv
pyenv install 3.11.0
pyenv global 3.11.0
python --version
```

バージョンが表示されれば正常です。

### Linuxの場合

Ubuntuの場合、次のコマンドでインストールできます。

```bash
sudo apt update
sudo apt install python3 python3-venv python3-pip
python3 --version
```

---

## 2. エディタ（開発ツール）の準備

開発に使うエディタとしては「Visual Studio Code（VS Code）」がおすすめです。

### VS Codeのインストール方法

1. [公式サイト](https://code.visualstudio.com/)からダウンロード
2. インストール後、「Python拡張機能」をVS Codeのマーケットプレースからインストール

他の選択肢として「PyCharm」もあり、本格的な開発環境が整います。

---

## 3. 仮想環境とは？

仮想環境とは、プロジェクトごとに独立したPython環境を作成する仕組みです。これによりライブラリのバージョン衝突を防げます。

### 仮想環境の作成と操作方法（Windows/Mac/Linux共通）

#### 仮想環境作成

```bash
python -m venv myenv
```

#### 仮想環境の有効化

* Windowsの場合

```cmd
myenv\Scripts\activate
```

* Mac/Linuxの場合

```bash
source myenv/bin/activate
```

仮想環境を有効にすると、プロンプトに仮想環境の名前が表示されます。

#### 仮想環境を抜ける

```bash
deactivate
```

---

## 4. パッケージ管理（pip）

Pythonのライブラリを追加する際に「pip」を使用します。

### pipコマンドの例

```bash
# requestsライブラリをインストール
pip install requests

# インストール済みパッケージ一覧表示
pip list

# 依存関係をファイルに出力
pip freeze > requirements.txt

# ファイルからパッケージを一括インストール
pip install -r requirements.txt
```

---

## 5. よくあるトラブルQ\&A

* **`python`コマンドが見つからない**

  * インストール時のPATH設定を再確認してください。
* **`pip`が使えない**

  * コマンドプロンプトまたはターミナルで次のコマンドを実行します。

```bash
python -m ensurepip --upgrade
```

* **仮想環境が有効化できない**

  * コマンドのパスやスペルを再確認してください。
* **日本語が文字化けする**

  * ファイルをUTF-8で保存し直してください。

---

> 分からないことは「エラー文をコピペして検索」も有効です。焦らず一歩ずつ進めましょう！

[変数と型](python_basic.md)

