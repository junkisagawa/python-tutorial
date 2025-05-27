# Watson X Orchestrate 開発環境のセットアップ

## このページで学ぶこと

Watson X Orchestrateの開発環境を構築します。COBOLの開発環境構築とは違い、いくつかのツールをインストールする必要がありますが、一つずつ丁寧に説明します。

## 必要なもの

### 1. システム要件
- **メモリ**: 16GB以上（COBOLプログラムより多く必要です）
- **CPU**: 8コア以上
- **ディスク容量**: 25GB以上の空き容量
- **OS**: Windows、Mac、またはLinux

### 2. ソフトウェア要件
- Python 3.11以上（3.13まで対応）
- Docker（コンテナ技術）
- テキストエディタ（VS Code推奨）

## ステップ1: Pythonの確認とインストール

### Pythonがインストールされているか確認

ターミナル（コマンドプロンプト）を開いて、以下のコマンドを実行します：

```bash
python --version
```

または

```bash
python3 --version
```

結果が `Python 3.11.x` 以上なら問題ありません。

### Pythonがない場合のインストール

1. [Python公式サイト](https://www.python.org/downloads/)にアクセス
2. 「Download Python 3.12.x」をクリック
3. インストーラーを実行
4. **重要**: 「Add Python to PATH」にチェックを入れる

## ステップ2: Dockerのインストール

Dockerは「アプリケーションをコンテナという箱に入れて動かす技術」です。COBOLで例えると、JCL（Job Control Language）のような役割を果たします。

### Macの場合

1. [Docker Desktop for Mac](https://www.docker.com/products/docker-desktop/)をダウンロード
2. インストーラーを実行
3. Dockerを起動

または、Homebrewを使用：

```bash
brew install --cask docker
```

### Windowsの場合

1. [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop/)をダウンロード
2. インストーラーを実行
3. 必要に応じてWSL2をセットアップ
4. Dockerを起動

### インストール確認

```bash
docker --version
```

## ステップ3: Watson X Orchestrate ADKのインストール

### 仮想環境の作成（推奨）

Pythonの仮想環境は、COBOLで言うところの「専用ライブラリ」のようなものです。プロジェクトごとに独立した環境を作ります。

```bash
# 作業ディレクトリを作成
mkdir watsonx-tutorial
cd watsonx-tutorial

# 仮想環境を作成
python -m venv venv

# 仮想環境を有効化（Mac/Linux）
source venv/bin/activate

# 仮想環境を有効化（Windows）
venv\Scripts\activate
```

### ADKのインストール

```bash
pip install --upgrade ibm-watsonx-orchestrate
```

### インストール確認

```bash
orchestrate --version
```

## ステップ4: 環境設定ファイルの作成

### .envファイルの作成

プロジェクトディレクトリに`.env`ファイルを作成します：

```bash
touch .env
```

以下の内容を`.env`ファイルに記述：

```env
# Watson X Orchestrate設定
ORCHESTRATE_DEV_MODE=true
ORCHESTRATE_LOG_LEVEL=INFO

# LLMモデル設定（ローカル開発用）
LLM_PROVIDER=local
LLM_MODEL=mock

# ポート設定
ORCHESTRATE_PORT=8080
```

💡 **COBOLプログラマーへの説明**：
`.env`ファイルは、COBOLの環境変数設定ファイルのようなものです。プログラムの動作に必要な設定値を記述します。

## ステップ5: ローカルサーバーの起動

### サーバーを起動

```bash
orchestrate server start -e .env
```

### 成功すると表示されるメッセージ

```
🚀 Watson X Orchestrate server is starting...
✅ Server started successfully on http://localhost:8080
📝 Logs are available at: ./logs/orchestrate.log
```

### サーバーの停止

別のターミナルウィンドウで：

```bash
orchestrate server stop
```

## ステップ6: 動作確認

### 環境のアクティベート

```bash
orchestrate env activate local
```

### ヘルスチェック

```bash
curl http://localhost:8080/health
```

正常な応答：
```json
{"status": "healthy", "version": "1.0.0"}
```

## トラブルシューティング

### よくある問題と解決方法

#### 1. Pythonのバージョンが古い
```
エラー: Python 3.11以上が必要です
```
**解決方法**: Pythonを最新版にアップデート

#### 2. Dockerが起動していない
```
エラー: Docker daemon is not running
```
**解決方法**: Docker Desktopを起動

#### 3. ポートが使用中
```
エラー: Port 8080 is already in use
```
**解決方法**: `.env`ファイルでポート番号を変更
```env
ORCHESTRATE_PORT=8081
```

## 練習問題

### 問題1: 環境確認スクリプトの作成

以下の内容で`check_env.py`を作成してください：

```python
import sys
import subprocess

def check_python():
    version = sys.version_info
    print(f"Python バージョン: {version.major}.{version.minor}.{version.micro}")
    if version.major >= 3 and version.minor >= 11:
        print("✅ Pythonのバージョンは問題ありません")
    else:
        print("❌ Python 3.11以上が必要です")

def check_docker():
    try:
        result = subprocess.run(['docker', '--version'], capture_output=True, text=True)
        print(f"Docker: {result.stdout.strip()}")
        print("✅ Dockerは正常にインストールされています")
    except:
        print("❌ Dockerがインストールされていません")

def check_orchestrate():
    try:
        result = subprocess.run(['orchestrate', '--version'], capture_output=True, text=True)
        print(f"Orchestrate: {result.stdout.strip()}")
        print("✅ Watson X Orchestrate ADKは正常にインストールされています")
    except:
        print("❌ Watson X Orchestrate ADKがインストールされていません")

if __name__ == "__main__":
    print("=== 環境チェック ===")
    check_python()
    check_docker()
    check_orchestrate()
```

### 解答

```bash
python check_env.py
```

期待される出力：
```
=== 環境チェック ===
Python バージョン: 3.12.0
✅ Pythonのバージョンは問題ありません
Docker: Docker version 24.0.0, build abcdef
✅ Dockerは正常にインストールされています
Orchestrate: orchestrate version 1.0.0
✅ Watson X Orchestrate ADKは正常にインストールされています
```

## 宿題

### 課題1: 環境構築ドキュメントの作成

自分の環境で実際にセットアップを行い、以下の内容を含むドキュメントを作成してください：

1. 遭遇した問題とその解決方法
2. セットアップにかかった時間
3. COBOLの開発環境構築と比較した感想
4. 改善提案があれば記載

### 課題2: Dockerコンテナの理解

以下のコマンドを実行し、結果を記録してください：

```bash
# 実行中のコンテナを確認
docker ps

# すべてのコンテナを確認
docker ps -a

# Dockerイメージを確認
docker images
```

それぞれのコマンドが何を表示しているか、COBOLの概念と比較して説明してください。

## まとめ

おめでとうございます！Watson X Orchestrateの開発環境が整いました。

次のステップでは、実際にAIエージェントを作成します。COBOLプログラムを書くのと同じように、一つずつ丁寧に進めていきましょう。

環境構築は最初は大変ですが、一度セットアップすれば後は簡単です。COBOLの開発環境も最初は大変だったことを思い出してください。新しい技術も同じです！