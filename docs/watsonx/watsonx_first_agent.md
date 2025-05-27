# はじめてのAIエージェント作成

## このページで学ぶこと

初めてのAIエージェントを作成します。まずは「挨拶ができるエージェント」から始めて、徐々に機能を追加していきます。

## エージェントとは？

### COBOLプログラムとの比較

| COBOLプログラム | AIエージェント |
|---------------|--------------|
| IDENTIFICATION DIVISION | エージェントの名前と説明 |
| DATA DIVISION | エージェントが使うツールと知識 |
| PROCEDURE DIVISION | エージェントへの指示（自然言語） |
| CALL文 | ツールの呼び出し |

## ステップ1: シンプルな挨拶エージェント

### エージェント設定ファイルの作成

`greeting-agent.yaml`という名前でファイルを作成します：

```yaml
spec_version: v1
style: react
name: greeting_agent
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "お客様に挨拶をする親切なエージェント"
instructions: |
  あなたは親切で礼儀正しい受付担当者です。
  
  以下のルールに従って対応してください：
  1. 常に丁寧な言葉遣いを心がける
  2. お客様の名前を聞いて、名前を使って挨拶する
  3. 時間帯に応じた挨拶をする（おはよう、こんにちは、こんばんは）
  4. お客様が困っていることがあれば、助けようとする
  
  対応例：
  - 朝: "おはようございます、[名前]様。本日はどのようなご用件でしょうか？"
  - 昼: "こんにちは、[名前]様。お待ちしておりました。"
  - 夜: "こんばんは、[名前]様。遅い時間にお疲れ様です。"
```

💡 **COBOLプログラマーへの説明**：
- `spec_version`: プログラムのバージョン管理のようなもの
- `name`: PROGRAM-IDに相当
- `llm`: 使用するAIモデル（COBOLでいうコンパイラの種類）
- `description`: プログラムの目的を説明
- `instructions`: PROCEDURE DIVISIONの処理内容を自然言語で記述

### エージェントの登録

ターミナルで以下のコマンドを実行：

```bash
# サーバーが起動していることを確認
orchestrate server start -e .env

# 環境をアクティベート
orchestrate env activate local

# エージェントをインポート
orchestrate agents import -f greeting-agent.yaml
```

成功すると：
```
✅ Agent 'greeting_agent' imported successfully
```

### エージェントとの対話

```bash
# チャットインターフェースを起動
orchestrate chat start
```

対話例：
```
You: こんにちは
Agent: こんにちは！私は受付担当のアシスタントです。お名前を教えていただけますか？

You: 山田です
Agent: こんにちは、山田様。本日はどのようなご用件でしょうか？

You: Watson Xについて教えてください
Agent: 山田様、Watson Xについてお尋ねですね。申し訳ございませんが、私は挨拶と基本的な受付業務を担当しております。Watson Xについての詳しい情報は、技術担当のスタッフにお繋ぎすることができます。よろしいでしょうか？
```

## ステップ2: 時間を認識するエージェント

### 改良版エージェントの作成

`greeting-agent-v2.yaml`を作成：

```yaml
spec_version: v1
style: react
name: greeting_agent_v2
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "時間を認識して適切な挨拶をするエージェント"
instructions: |
  あなたは親切で礼儀正しい受付担当者です。
  
  重要な指示：
  1. 現在の時刻を確認して、時間帯に応じた挨拶をする
  2. 営業時間（9:00-18:00）を把握し、時間外の場合は案内する
  3. お客様の待ち時間を気遣う
  
  時間帯別の挨拶：
  - 5:00-10:00: おはようございます
  - 10:00-17:00: こんにちは
  - 17:00-5:00: こんばんは
  
  営業時間外の対応：
  - "申し訳ございません。現在は営業時間外です。"
  - "営業時間は平日9:00-18:00となっております。"
tools:
  - get_current_time
```

### 時刻取得ツールの作成

`time_tool.py`を作成：

```python
from datetime import datetime
import pytz

def get_current_time():
    """
    現在の日本時間を取得する
    
    Returns:
        str: 現在の日時（例: "2024年3月15日 14時30分"）
    """
    # 日本のタイムゾーンを設定
    japan_tz = pytz.timezone('Asia/Tokyo')
    current_time = datetime.now(japan_tz)
    
    # 日本語形式で返す
    return current_time.strftime("%Y年%m月%d日 %H時%M分")
```

### ツールの登録

```bash
# ツールをインポート
orchestrate tools import -k python -f time_tool.py

# 新しいエージェントをインポート
orchestrate agents import -f greeting-agent-v2.yaml
```

## ステップ3: 業務知識を持つエージェント

### 営業時間と料金を案内するエージェント

`business-agent.yaml`を作成：

```yaml
spec_version: v1
style: react
name: business_info_agent
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "ビジネス情報を案内するエージェント"
instructions: |
  あなたはABC会社の受付担当者です。
  
  会社情報：
  - 営業時間: 平日 9:00-18:00
  - 定休日: 土日祝日
  - 電話番号: 03-1234-5678
  - メール: info@abc-company.jp
  
  サービス料金：
  - 基本プラン: 月額10,000円
  - スタンダードプラン: 月額30,000円
  - プレミアムプラン: 月額50,000円
  
  よくある質問への回答：
  1. 無料体験期間は30日間
  2. 解約は1ヶ月前に連絡が必要
  3. 支払い方法はクレジットカードと銀行振込
  
  対応のポイント：
  - 正確な情報を提供する
  - 不明な点は「確認いたします」と答える
  - 料金の説明は分かりやすく
tools:
  - get_current_time
  - check_business_hours
```

### 営業時間チェックツール

`business_tools.py`を作成：

```python
from datetime import datetime
import pytz

def check_business_hours():
    """
    現在が営業時間内かどうかをチェックする
    
    Returns:
        dict: 営業状態の情報
    """
    japan_tz = pytz.timezone('Asia/Tokyo')
    current_time = datetime.now(japan_tz)
    
    # 曜日を取得（0=月曜日, 6=日曜日）
    weekday = current_time.weekday()
    hour = current_time.hour
    
    # 営業時間の判定
    is_weekday = weekday < 5  # 月〜金
    is_business_hour = 9 <= hour < 18
    
    if is_weekday and is_business_hour:
        status = "営業中"
        message = "ただいま営業時間内です。"
    elif is_weekday and hour < 9:
        status = "営業時間前"
        message = f"本日の営業開始は9:00からです。"
    elif is_weekday and hour >= 18:
        status = "営業時間終了"
        message = "本日の営業は終了しました。"
    else:
        status = "休業日"
        message = "本日は休業日です。平日9:00-18:00に営業しております。"
    
    return {
        "status": status,
        "message": message,
        "current_time": current_time.strftime("%Y年%m月%d日 %H時%M分"),
        "is_open": status == "営業中"
    }

def calculate_price(plan_type, months):
    """
    プランと期間から料金を計算する
    
    Args:
        plan_type (str): プランの種類（basic/standard/premium）
        months (int): 契約月数
    
    Returns:
        dict: 料金情報
    """
    prices = {
        "basic": 10000,
        "standard": 30000,
        "premium": 50000
    }
    
    if plan_type not in prices:
        return {"error": "無効なプランです"}
    
    monthly_price = prices[plan_type]
    total_price = monthly_price * months
    
    # 長期割引
    discount = 0
    if months >= 12:
        discount = 0.1  # 10%割引
    elif months >= 6:
        discount = 0.05  # 5%割引
    
    discounted_price = total_price * (1 - discount)
    
    return {
        "plan": plan_type,
        "months": months,
        "monthly_price": monthly_price,
        "total_price": total_price,
        "discount_rate": discount,
        "discount_amount": total_price * discount,
        "final_price": discounted_price
    }
```

## 練習問題

### 問題1: カスタマーサポートエージェントの作成

以下の要件を満たすエージェントを作成してください：

1. お客様の問題を聞き取る
2. 問題のカテゴリを判定（技術的、請求、一般）
3. 適切な部署を案内

**ヒント**: instructionsに問題カテゴリの判定基準を記述

### 解答例

`support-agent.yaml`:

```yaml
spec_version: v1
style: react
name: customer_support_agent
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "お客様の問題を適切な部署に振り分けるエージェント"
instructions: |
  あなたはカスタマーサポートの初期対応担当者です。
  
  対応手順：
  1. お客様の問題を丁寧に聞き取る
  2. 問題をカテゴリ分けする
  3. 適切な部署を案内する
  
  カテゴリ分類：
  - 技術的問題: ログインできない、エラーが出る、動作が遅い
    → 技術サポート部（内線: 2001）
  
  - 請求関連: 料金、支払い、領収書、解約
    → 経理部（内線: 3001）
  
  - 一般的な質問: 使い方、機能について、営業時間
    → 総合案内（内線: 1001）
  
  対応例：
  Customer: "ログインができません"
  You: "ログインでお困りですね。技術的な問題のようですので、技術サポート部（内線2001）にお繋ぎいたします。"
tools:
  - get_current_time
  - log_customer_issue
```

### 問題2: 複数言語対応エージェント

日本語と英語の両方で対応できるエージェントを作成してください。

**要件**:
- 最初の挨拶で言語を判定
- その言語で一貫して対応
- 言語切り替えリクエストに対応

## 宿題

### 課題1: 予約受付エージェントの作成

以下の機能を持つ予約受付エージェントを作成してください：

1. お客様の名前と連絡先を聞く
2. 希望日時を確認
3. サービスの種類を選択してもらう
4. 予約内容を確認して番号を発行

必要なファイル：
- `reservation-agent.yaml`
- `reservation_tools.py`（予約番号生成ツール）

### 課題2: エージェントの改善提案

作成した挨拶エージェントを実際に使ってみて、以下の点について改善提案を書いてください：

1. 現在の問題点（3つ以上）
2. 改善案（具体的に）
3. COBOLプログラムと比較した利点と欠点

### 課題3: エラー処理の実装

エージェントが以下の状況に対応できるように改良してください：

1. お客様が無関係な質問をした場合
2. 不適切な言葉を使った場合
3. 同じ質問を繰り返した場合

## まとめ

初めてのAIエージェント作成、お疲れ様でした！

学んだこと：
- エージェントの基本構造（YAML設定）
- 自然言語での指示の書き方
- ツールとの連携方法
- 実用的なエージェントの設計

COBOLプログラムと違い、AIエージェントは：
- 自然な会話ができる
- 文脈を理解できる
- 柔軟な対応が可能

次のステップでは、エージェントが使えるツールを自分で作成する方法を学びます。COBOLのサブルーチンを書くような感覚で、エージェントの機能を拡張していきましょう！