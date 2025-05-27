# Watson X Orchestrate 実践演習

## このページで学ぶこと

これまで学んだ知識を活用して、実践的な課題に取り組みます。各課題には解答例も用意していますが、まずは自分で考えてみましょう。

## 演習1: ヘルプデスクシステムの構築

### 課題

社内ヘルプデスクシステムを構築してください。

**要件**：
1. IT機器のトラブル対応
2. ソフトウェアの使い方案内
3. パスワードリセット対応
4. 機器の貸出管理

### ヒント

- 問題の種類を分類するエージェント
- 各分野の専門エージェント
- 履歴を記録するツール

### 解答例

#### メインコーディネーター

`helpdesk-coordinator.yaml`:

```yaml
spec_version: v1
style: react
name: helpdesk_coordinator
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "社内ヘルプデスクの総合窓口"
instructions: |
  あなたは社内ヘルプデスクのコーディネーターです。
  
  対応手順：
  1. 従業員からの問い合わせを受付
  2. 問題の種類を判別
  3. 適切な専門エージェントに振り分け
  4. 解決状況を追跡
  
  問題カテゴリ：
  - ハードウェア: PC、プリンター、ネットワーク機器
  - ソフトウェア: Office、業務システム、ブラウザ
  - アカウント: パスワード、アクセス権限
  - 貸出: 機器の借用、返却
  
  重要事項：
  - 緊急度を判断（高・中・低）
  - チケット番号を発行
  - 解決までの予想時間を伝える
tools:
  - create_ticket
  - get_ticket_status
  - update_ticket
collaborators:
  - hardware_support_agent
  - software_support_agent
  - account_support_agent
  - equipment_rental_agent
```

#### ハードウェアサポートエージェント

`hardware-support-agent.yaml`:

```yaml
spec_version: v1
style: react
name: hardware_support_agent
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "ハードウェアトラブル専門エージェント"
instructions: |
  ハードウェアの問題を診断し、解決策を提供します。
  
  対応機器：
  - デスクトップPC、ノートPC
  - プリンター、スキャナー
  - ネットワーク機器
  - モニター、キーボード、マウス
  
  トラブルシューティング手順：
  1. 症状の詳細確認
  2. 基本的な対処法を案内
  3. 解決しない場合は現地対応を手配
  
  よくある問題と対処法：
  - 電源が入らない: 電源ケーブル確認、コンセント確認
  - 印刷できない: ドライバ確認、用紙・トナー確認
  - ネットワーク接続不可: ケーブル確認、IP設定確認
tools:
  - check_device_status
  - schedule_onsite_support
  - order_replacement_parts
```

#### サポートツール

`helpdesk_tools.py`:

```python
import json
import uuid
from datetime import datetime
from typing import Dict, List, Optional

# チケット管理
TICKETS_FILE = "helpdesk_tickets.json"

def create_ticket(
    user_name: str,
    department: str,
    issue_type: str,
    description: str,
    priority: str = "medium"
) -> Dict:
    """
    新しいサポートチケットを作成
    
    Args:
        user_name (str): 申請者名
        department (str): 部署
        issue_type (str): 問題の種類
        description (str): 問題の詳細
        priority (str): 優先度（high/medium/low）
    
    Returns:
        Dict: 作成されたチケット情報
    """
    tickets = _load_tickets()
    
    ticket_id = f"HD{datetime.now().strftime('%Y%m%d')}{str(uuid.uuid4())[:4].upper()}"
    
    new_ticket = {
        "ticket_id": ticket_id,
        "user_name": user_name,
        "department": department,
        "issue_type": issue_type,
        "description": description,
        "priority": priority,
        "status": "open",
        "created_at": datetime.now().isoformat(),
        "updated_at": datetime.now().isoformat(),
        "assigned_to": None,
        "resolution": None
    }
    
    tickets.append(new_ticket)
    _save_tickets(tickets)
    
    # 優先度に応じた対応時間の目安
    response_times = {
        "high": "1時間以内",
        "medium": "4時間以内",
        "low": "1営業日以内"
    }
    
    return {
        "ticket_id": ticket_id,
        "message": f"チケットを作成しました。予想対応時間: {response_times[priority]}",
        "ticket": new_ticket
    }

def get_ticket_status(ticket_id: str) -> Dict:
    """
    チケットのステータスを確認
    
    Args:
        ticket_id (str): チケットID
    
    Returns:
        Dict: チケットの現在の状態
    """
    tickets = _load_tickets()
    
    for ticket in tickets:
        if ticket["ticket_id"] == ticket_id:
            return {
                "found": True,
                "ticket_id": ticket_id,
                "status": ticket["status"],
                "assigned_to": ticket["assigned_to"],
                "created_at": ticket["created_at"],
                "updated_at": ticket["updated_at"],
                "resolution": ticket["resolution"]
            }
    
    return {"found": False, "error": "チケットが見つかりません"}

def check_device_status(device_type: str, device_id: str) -> Dict:
    """
    デバイスの状態を確認（ダミー実装）
    
    Args:
        device_type (str): デバイスの種類
        device_id (str): デバイスID
    
    Returns:
        Dict: デバイスの状態
    """
    # 実際の実装では、監視システムのAPIを呼び出す
    device_statuses = {
        "printer": {
            "status": "warning",
            "details": {
                "toner_level": 15,
                "paper_level": 80,
                "error_code": None,
                "last_maintenance": "2024-01-15"
            }
        },
        "pc": {
            "status": "online",
            "details": {
                "cpu_usage": 45,
                "memory_usage": 62,
                "disk_usage": 78,
                "last_update": "2024-03-20"
            }
        }
    }
    
    if device_type.lower() in device_statuses:
        return {
            "device_type": device_type,
            "device_id": device_id,
            **device_statuses[device_type.lower()]
        }
    
    return {"error": "デバイスタイプが不明です"}

def _load_tickets() -> List[Dict]:
    """チケットデータを読み込む"""
    if os.path.exists(TICKETS_FILE):
        with open(TICKETS_FILE, 'r', encoding='utf-8') as f:
            return json.load(f)
    return []

def _save_tickets(tickets: List[Dict]):
    """チケットデータを保存"""
    with open(TICKETS_FILE, 'w', encoding='utf-8') as f:
        json.dump(tickets, f, ensure_ascii=False, indent=2)
```

## 演習2: 在庫管理と発注システム

### 課題

商品の在庫管理と自動発注を行うシステムを構築してください。

**要件**：
1. 在庫数の確認と更新
2. 在庫が少なくなったら自動発注
3. 発注履歴の管理
4. 需要予測に基づく発注量の提案

### ヒント

- 在庫監視エージェント
- 発注管理エージェント
- 分析エージェント

### 解答例

#### 在庫管理ツール

`inventory_management_tools.py`:

```python
import json
import os
from datetime import datetime, timedelta
from typing import Dict, List, Optional
import statistics

INVENTORY_FILE = "inventory_data.json"
ORDER_HISTORY_FILE = "order_history.json"

def analyze_demand(product_id: str, days: int = 30) -> Dict:
    """
    過去の販売データから需要を分析
    
    Args:
        product_id (str): 商品ID
        days (int): 分析期間（日数）
    
    Returns:
        Dict: 需要分析結果
    """
    # 実際の実装では販売履歴DBから取得
    # ここではダミーデータで実装
    sales_data = [12, 15, 10, 18, 20, 14, 16, 22, 19, 17]  # 過去10日間の販売数
    
    daily_average = statistics.mean(sales_data)
    daily_std = statistics.stdev(sales_data)
    max_daily = max(sales_data)
    
    # 安全在庫 = 平均需要 × リードタイム + 安全係数 × 標準偏差
    lead_time = 3  # 発注から納品まで3日
    safety_factor = 1.65  # 95%のサービスレベル
    
    safety_stock = (daily_average * lead_time) + (safety_factor * daily_std * (lead_time ** 0.5))
    reorder_point = daily_average * lead_time + safety_stock
    order_quantity = daily_average * 7  # 1週間分を発注
    
    return {
        "product_id": product_id,
        "daily_average": round(daily_average, 1),
        "daily_std": round(daily_std, 1),
        "max_daily_sales": max_daily,
        "recommended_safety_stock": round(safety_stock),
        "recommended_reorder_point": round(reorder_point),
        "recommended_order_quantity": round(order_quantity),
        "analysis_period_days": len(sales_data)
    }

def check_inventory_levels() -> List[Dict]:
    """
    全商品の在庫レベルをチェックし、発注が必要な商品をリストアップ
    
    Returns:
        List[Dict]: 発注が必要な商品リスト
    """
    inventory = _load_inventory()
    reorder_needed = []
    
    for product_id, data in inventory.items():
        current_stock = data.get("quantity", 0)
        reorder_point = data.get("reorder_point", 10)
        
        if current_stock <= reorder_point:
            # 需要分析を実行
            demand_analysis = analyze_demand(product_id)
            
            reorder_needed.append({
                "product_id": product_id,
                "product_name": data.get("name", "Unknown"),
                "current_stock": current_stock,
                "reorder_point": reorder_point,
                "recommended_quantity": demand_analysis["recommended_order_quantity"],
                "urgency": "high" if current_stock < reorder_point * 0.5 else "medium"
            })
    
    return sorted(reorder_needed, key=lambda x: x["urgency"], reverse=True)

def create_purchase_order(product_id: str, quantity: int, supplier: str) -> Dict:
    """
    発注書を作成
    
    Args:
        product_id (str): 商品ID
        quantity (int): 発注数量
        supplier (str): 仕入先
    
    Returns:
        Dict: 発注情報
    """
    orders = _load_orders()
    inventory = _load_inventory()
    
    if product_id not in inventory:
        return {"error": "商品が見つかりません"}
    
    order_id = f"PO{datetime.now().strftime('%Y%m%d%H%M%S')}"
    unit_price = inventory[product_id].get("unit_price", 0)
    total_amount = unit_price * quantity
    
    new_order = {
        "order_id": order_id,
        "product_id": product_id,
        "product_name": inventory[product_id].get("name"),
        "quantity": quantity,
        "unit_price": unit_price,
        "total_amount": total_amount,
        "supplier": supplier,
        "status": "pending",
        "order_date": datetime.now().isoformat(),
        "expected_delivery": (datetime.now() + timedelta(days=3)).isoformat()
    }
    
    orders.append(new_order)
    _save_orders(orders)
    
    return {
        "success": True,
        "order_id": order_id,
        "message": f"発注書を作成しました。納期予定: {new_order['expected_delivery'][:10]}",
        "order": new_order
    }

def _load_inventory() -> Dict:
    """在庫データを読み込む"""
    if os.path.exists(INVENTORY_FILE):
        with open(INVENTORY_FILE, 'r', encoding='utf-8') as f:
            return json.load(f)
    
    # サンプルデータ
    return {
        "PROD001": {
            "name": "ノートPC",
            "quantity": 5,
            "unit_price": 80000,
            "reorder_point": 10,
            "supplier": "テックサプライ社"
        },
        "PROD002": {
            "name": "マウス",
            "quantity": 25,
            "unit_price": 2000,
            "reorder_point": 20,
            "supplier": "PC周辺機器社"
        }
    }

def _load_orders() -> List[Dict]:
    """発注履歴を読み込む"""
    if os.path.exists(ORDER_HISTORY_FILE):
        with open(ORDER_HISTORY_FILE, 'r', encoding='utf-8') as f:
            return json.load(f)
    return []

def _save_orders(orders: List[Dict]):
    """発注履歴を保存"""
    with open(ORDER_HISTORY_FILE, 'w', encoding='utf-8') as f:
        json.dump(orders, f, ensure_ascii=False, indent=2)
```

#### 在庫管理エージェント

`inventory-management-agent.yaml`:

```yaml
spec_version: v1
style: react
name: inventory_manager
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "在庫管理と発注を自動化するエージェント"
instructions: |
  在庫レベルを監視し、適切なタイミングで発注を提案・実行します。
  
  主な業務：
  1. 定期的な在庫チェック
  2. 需要予測に基づく発注量の計算
  3. 発注書の作成と承認依頼
  4. 納品確認と在庫更新
  
  発注ルール：
  - 在庫が発注点を下回ったら発注検討
  - 緊急度に応じて発注方法を変更
  - 需要予測を考慮した最適発注量
  
  レポート機能：
  - 日次: 在庫状況サマリー
  - 週次: 発注履歴と納品状況
  - 月次: 在庫回転率と最適化提案
tools:
  - check_inventory_levels
  - analyze_demand
  - create_purchase_order
  - update_stock
collaborators:
  - purchasing_approval_agent
  - supplier_communication_agent
```

## 演習3: 多言語カスタマーサポート

### 課題

日本語、英語、中国語に対応したカスタマーサポートシステムを構築してください。

**要件**：
1. 言語を自動判定
2. 各言語専門のエージェント
3. 翻訳機能
4. 文化的な配慮

### ヒント

- 言語判定ツール
- 各言語のテンプレート
- 翻訳API連携

### 解答例

`multilingual_tools.py`:

```python
def detect_language(text: str) -> Dict:
    """
    テキストの言語を判定（簡易実装）
    
    Args:
        text (str): 判定対象のテキスト
    
    Returns:
        Dict: 言語判定結果
    """
    # 簡易的な判定ロジック
    if any(char in text for char in "あいうえおかきくけこ"):
        return {"language": "ja", "confidence": 0.95}
    elif any('\u4e00' <= char <= '\u9fff' for char in text):
        return {"language": "zh", "confidence": 0.90}
    elif text.isascii():
        return {"language": "en", "confidence": 0.85}
    else:
        return {"language": "unknown", "confidence": 0.0}

def translate_text(text: str, source_lang: str, target_lang: str) -> Dict:
    """
    テキストを翻訳（ダミー実装）
    
    Args:
        text (str): 翻訳するテキスト
        source_lang (str): 元の言語
        target_lang (str): 翻訳先言語
    
    Returns:
        Dict: 翻訳結果
    """
    # 実際はGoogle Translate APIなどを使用
    translations = {
        ("ja", "en"): {
            "こんにちは": "Hello",
            "ありがとうございます": "Thank you",
            "申し訳ございません": "I apologize"
        },
        ("ja", "zh"): {
            "こんにちは": "你好",
            "ありがとうございます": "谢谢",
            "申し訳ございません": "对不起"
        }
    }
    
    translation_dict = translations.get((source_lang, target_lang), {})
    translated = translation_dict.get(text, f"[Translation of: {text}]")
    
    return {
        "original": text,
        "translated": translated,
        "source_lang": source_lang,
        "target_lang": target_lang
    }
```

## 演習4: スマートスケジューリングシステム

### 課題

会議室予約と参加者のスケジュール調整を行うシステムを構築してください。

**要件**：
1. 参加者全員の空き時間を検索
2. 最適な会議室を提案
3. リマインダー送信
4. リスケジュール機能

### ヒント

- カレンダーAPI連携
- 最適化アルゴリズム
- 通知システム

## 総合演習: ECサイトのAIアシスタント

### 課題

オンラインショッピングサイト用の総合AIアシスタントを構築してください。

**必要な機能**：
1. 商品検索と推薦
2. 在庫確認
3. 注文処理
4. 配送状況追跡
5. 返品・交換対応
6. レビュー分析

### 実装のポイント

1. **エージェントの構成**
   - メインコーディネーター
   - 商品推薦エージェント
   - 注文処理エージェント
   - カスタマーサービスエージェント
   - 分析エージェント

2. **必要なツール**
   - 商品データベース検索
   - 在庫管理システム連携
   - 決済処理
   - 配送会社API連携
   - レビュー感情分析

3. **考慮事項**
   - セキュリティ（個人情報保護）
   - トランザクション管理
   - エラーハンドリング
   - スケーラビリティ

## 宿題: 実践プロジェクト

### プロジェクト選択

以下から1つ選んで実装してください：

1. **病院予約システム**
   - 診療科選択
   - 医師指名
   - 症状ヒアリング
   - 予約確認

2. **人事採用支援システム**
   - 履歴書スクリーニング
   - 面接スケジューリング
   - 候補者評価
   - オファー作成

3. **不動産物件案内システム**
   - 条件ヒアリング
   - 物件検索
   - 内覧予約
   - 契約サポート

### 提出物

1. 全エージェントのYAMLファイル
2. 必要なツールのPythonコード
3. システム設計書
4. デモ動画または実行ログ
5. 改善提案書

## まとめ

実践的な演習を通じて、Watson X Orchestrateの活用方法を学びました。

学んだこと：
- 実際の業務課題の分析
- エージェントとツールの設計
- システム統合の考え方
- エラー処理とセキュリティ

これらの知識を活かして、自社の業務改善に挑戦してみてください！