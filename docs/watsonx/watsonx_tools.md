# エージェント用ツールの開発

## このページで学ぶこと

AIエージェントが使える「ツール」を自分で作成する方法を学びます。ツールは、エージェントに特定の機能を提供するPython関数です。

## ツールとは？

### COBOLとの比較で理解する

| COBOLの概念 | Watson X Orchestrateのツール |
|------------|---------------------------|
| CALLで呼ぶサブプログラム | Pythonで書いたツール関数 |
| COPY句で取り込むライブラリ | importするPythonモジュール |
| ファイルI/O（READ/WRITE） | データベースアクセスツール |
| COMPUTE文での計算 | 計算処理ツール |

ツールは、エージェントが「できること」を増やすための拡張機能です。

## ツールの基本構造

### シンプルなツールの例

```python
def add_numbers(a: int, b: int) -> int:
    """
    2つの数値を足し算する
    
    Args:
        a (int): 1つ目の数値
        b (int): 2つ目の数値
    
    Returns:
        int: 足し算の結果
    """
    return a + b
```

💡 **重要なポイント**：
1. **関数名**: わかりやすい名前をつける
2. **型ヒント**: 引数と戻り値の型を明示
3. **docstring**: 関数の説明を詳しく書く（エージェントはこれを読んで使い方を理解）

## ステップ1: 計算ツールの作成

### 基本的な計算ツール

`calculator_tools.py`を作成：

```python
def calculate_tax(price: float, tax_rate: float = 0.1) -> dict:
    """
    商品価格から税込み価格を計算する
    
    Args:
        price (float): 税抜き価格
        tax_rate (float): 税率（デフォルト10%）
    
    Returns:
        dict: 計算結果を含む辞書
            - price: 税抜き価格
            - tax_amount: 税額
            - total: 税込み価格
    """
    tax_amount = price * tax_rate
    total = price + tax_amount
    
    return {
        "price": price,
        "tax_amount": round(tax_amount, 2),
        "total": round(total, 2),
        "tax_rate_percent": tax_rate * 100
    }

def calculate_discount(original_price: float, discount_percent: float) -> dict:
    """
    割引価格を計算する
    
    Args:
        original_price (float): 元の価格
        discount_percent (float): 割引率（%）
    
    Returns:
        dict: 割引計算の結果
    """
    if discount_percent < 0 or discount_percent > 100:
        return {"error": "割引率は0〜100の間で指定してください"}
    
    discount_rate = discount_percent / 100
    discount_amount = original_price * discount_rate
    discounted_price = original_price - discount_amount
    
    return {
        "original_price": original_price,
        "discount_percent": discount_percent,
        "discount_amount": round(discount_amount, 2),
        "discounted_price": round(discounted_price, 2)
    }

def calculate_installment(total_price: float, months: int, interest_rate: float = 0) -> dict:
    """
    分割払いの月額を計算する
    
    Args:
        total_price (float): 総額
        months (int): 分割回数
        interest_rate (float): 年利（%、デフォルト0）
    
    Returns:
        dict: 分割払いの計算結果
    """
    if months <= 0:
        return {"error": "分割回数は1以上を指定してください"}
    
    # 金利がある場合の計算
    if interest_rate > 0:
        monthly_rate = interest_rate / 100 / 12
        monthly_payment = total_price * (monthly_rate * (1 + monthly_rate)**months) / ((1 + monthly_rate)**months - 1)
        total_payment = monthly_payment * months
        interest_amount = total_payment - total_price
    else:
        monthly_payment = total_price / months
        total_payment = total_price
        interest_amount = 0
    
    return {
        "total_price": total_price,
        "months": months,
        "monthly_payment": round(monthly_payment, 2),
        "total_payment": round(total_payment, 2),
        "interest_amount": round(interest_amount, 2),
        "interest_rate": interest_rate
    }
```

### ツールの登録とテスト

```bash
# ツールをインポート
orchestrate tools import -k python -f calculator_tools.py

# ツールの一覧を確認
orchestrate tools list
```

## ステップ2: データベース連携ツール

### 顧客情報管理ツール

`customer_tools.py`を作成：

```python
import json
import os
from typing import Optional, List, Dict
from datetime import datetime

# 簡易的なJSONデータベース
CUSTOMER_DB_FILE = "customers.json"

def _load_customers() -> List[Dict]:
    """顧客データを読み込む（内部関数）"""
    if os.path.exists(CUSTOMER_DB_FILE):
        with open(CUSTOMER_DB_FILE, 'r', encoding='utf-8') as f:
            return json.load(f)
    return []

def _save_customers(customers: List[Dict]):
    """顧客データを保存する（内部関数）"""
    with open(CUSTOMER_DB_FILE, 'w', encoding='utf-8') as f:
        json.dump(customers, f, ensure_ascii=False, indent=2)

def search_customer(search_term: str) -> List[Dict]:
    """
    顧客を検索する
    
    Args:
        search_term (str): 検索キーワード（名前、電話番号、メールアドレス）
    
    Returns:
        List[Dict]: 検索結果のリスト
    """
    customers = _load_customers()
    results = []
    
    search_term_lower = search_term.lower()
    
    for customer in customers:
        if (search_term_lower in customer.get('name', '').lower() or
            search_term in customer.get('phone', '') or
            search_term_lower in customer.get('email', '').lower()):
            results.append(customer)
    
    return results

def add_customer(name: str, phone: str, email: str, notes: Optional[str] = None) -> Dict:
    """
    新規顧客を追加する
    
    Args:
        name (str): 顧客名
        phone (str): 電話番号
        email (str): メールアドレス
        notes (Optional[str]): 備考
    
    Returns:
        Dict: 追加された顧客情報
    """
    customers = _load_customers()
    
    # 重複チェック
    for customer in customers:
        if customer['email'] == email:
            return {"error": "このメールアドレスは既に登録されています"}
    
    # 新規顧客ID生成
    customer_id = len(customers) + 1
    
    new_customer = {
        "id": customer_id,
        "name": name,
        "phone": phone,
        "email": email,
        "notes": notes or "",
        "created_at": datetime.now().isoformat(),
        "updated_at": datetime.now().isoformat()
    }
    
    customers.append(new_customer)
    _save_customers(customers)
    
    return new_customer

def get_customer_by_id(customer_id: int) -> Optional[Dict]:
    """
    IDで顧客情報を取得する
    
    Args:
        customer_id (int): 顧客ID
    
    Returns:
        Optional[Dict]: 顧客情報（見つからない場合はNone）
    """
    customers = _load_customers()
    
    for customer in customers:
        if customer['id'] == customer_id:
            return customer
    
    return None

def update_customer_notes(customer_id: int, notes: str) -> Dict:
    """
    顧客の備考を更新する
    
    Args:
        customer_id (int): 顧客ID
        notes (str): 新しい備考
    
    Returns:
        Dict: 更新結果
    """
    customers = _load_customers()
    
    for customer in customers:
        if customer['id'] == customer_id:
            customer['notes'] = notes
            customer['updated_at'] = datetime.now().isoformat()
            _save_customers(customers)
            return {"success": True, "message": "備考を更新しました", "customer": customer}
    
    return {"error": "指定された顧客が見つかりません"}
```

## ステップ3: 外部API連携ツール

### 天気情報取得ツール

`weather_tools.py`を作成：

```python
import requests
from typing import Dict, Optional

def get_weather_info(city: str) -> Dict:
    """
    指定された都市の天気情報を取得する（ダミー実装）
    
    Args:
        city (str): 都市名（例: "東京", "大阪"）
    
    Returns:
        Dict: 天気情報
    """
    # 実際のAPIではなく、ダミーデータを返す
    # 本番環境では、OpenWeatherMap APIなどを使用
    
    weather_data = {
        "東京": {
            "temperature": 22,
            "condition": "晴れ",
            "humidity": 65,
            "wind_speed": 3.5
        },
        "大阪": {
            "temperature": 24,
            "condition": "曇り",
            "humidity": 70,
            "wind_speed": 2.8
        },
        "札幌": {
            "temperature": 15,
            "condition": "雨",
            "humidity": 80,
            "wind_speed": 4.2
        }
    }
    
    if city in weather_data:
        data = weather_data[city]
        return {
            "city": city,
            "temperature": data["temperature"],
            "condition": data["condition"],
            "humidity": data["humidity"],
            "wind_speed": data["wind_speed"],
            "description": f"{city}の現在の天気は{data['condition']}、気温は{data['temperature']}度です。"
        }
    else:
        return {
            "error": f"{city}の天気情報は取得できません",
            "available_cities": list(weather_data.keys())
        }

def get_exchange_rate(from_currency: str, to_currency: str, amount: float = 1) -> Dict:
    """
    為替レートを取得して換算する（ダミー実装）
    
    Args:
        from_currency (str): 変換元通貨（例: "JPY", "USD"）
        to_currency (str): 変換先通貨
        amount (float): 変換する金額
    
    Returns:
        Dict: 為替レート情報と換算結果
    """
    # ダミーの為替レート
    rates = {
        "USD": {"JPY": 150.5, "EUR": 0.92, "GBP": 0.79},
        "JPY": {"USD": 0.00664, "EUR": 0.00611, "GBP": 0.00525},
        "EUR": {"USD": 1.087, "JPY": 163.6, "GBP": 0.859}
    }
    
    from_currency = from_currency.upper()
    to_currency = to_currency.upper()
    
    if from_currency == to_currency:
        return {
            "from_currency": from_currency,
            "to_currency": to_currency,
            "rate": 1.0,
            "amount": amount,
            "converted_amount": amount
        }
    
    if from_currency in rates and to_currency in rates[from_currency]:
        rate = rates[from_currency][to_currency]
        converted = amount * rate
        
        return {
            "from_currency": from_currency,
            "to_currency": to_currency,
            "rate": rate,
            "amount": amount,
            "converted_amount": round(converted, 2),
            "description": f"{amount} {from_currency} = {round(converted, 2)} {to_currency}"
        }
    else:
        return {
            "error": f"{from_currency}から{to_currency}への為替レートは利用できません"
        }
```

## ステップ4: ツールを使うエージェント

### 営業支援エージェント

`sales-agent.yaml`を作成：

```yaml
spec_version: v1
style: react
name: sales_support_agent
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "営業活動を支援するエージェント"
instructions: |
  あなたは優秀な営業支援アシスタントです。
  
  主な業務：
  1. 顧客情報の管理
  2. 見積もりの作成
  3. 為替レートの確認
  4. 天気情報の提供（訪問時の参考）
  
  対応のポイント：
  - 正確な計算を心がける
  - 顧客情報は丁寧に扱う
  - 見積もりには税込み価格を明記
  - 分割払いの提案も積極的に行う
  
  ツールの使い方：
  - calculate_tax: 税込み価格の計算
  - calculate_discount: 割引価格の計算
  - calculate_installment: 分割払いの計算
  - search_customer: 顧客検索
  - add_customer: 新規顧客登録
  - get_weather_info: 天気情報取得
  - get_exchange_rate: 為替レート確認
tools:
  - calculate_tax
  - calculate_discount
  - calculate_installment
  - search_customer
  - add_customer
  - get_customer_by_id
  - update_customer_notes
  - get_weather_info
  - get_exchange_rate
```

## エラー処理とベストプラクティス

### 1. 入力値の検証

```python
def safe_divide(numerator: float, denominator: float) -> Dict:
    """
    安全な除算を行う
    
    Args:
        numerator (float): 分子
        denominator (float): 分母
    
    Returns:
        Dict: 計算結果またはエラー
    """
    if denominator == 0:
        return {"error": "0で除算することはできません"}
    
    result = numerator / denominator
    return {
        "numerator": numerator,
        "denominator": denominator,
        "result": round(result, 4)
    }
```

### 2. 例外処理

```python
def read_file_safely(file_path: str) -> Dict:
    """
    ファイルを安全に読み込む
    
    Args:
        file_path (str): ファイルパス
    
    Returns:
        Dict: ファイル内容またはエラー
    """
    try:
        with open(file_path, 'r', encoding='utf-8') as f:
            content = f.read()
        return {
            "success": True,
            "content": content,
            "file_path": file_path
        }
    except FileNotFoundError:
        return {"error": f"ファイルが見つかりません: {file_path}"}
    except PermissionError:
        return {"error": f"ファイルの読み取り権限がありません: {file_path}"}
    except Exception as e:
        return {"error": f"予期しないエラーが発生しました: {str(e)}"}
```

### 3. ログ記録

```python
import logging
from datetime import datetime

# ログ設定
logging.basicConfig(
    filename='agent_tools.log',
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)

def process_order(order_id: str, action: str) -> Dict:
    """
    注文を処理する（ログ記録付き）
    
    Args:
        order_id (str): 注文ID
        action (str): 実行するアクション
    
    Returns:
        Dict: 処理結果
    """
    logger = logging.getLogger(__name__)
    
    try:
        # 処理開始をログに記録
        logger.info(f"注文処理開始: order_id={order_id}, action={action}")
        
        # ここで実際の処理を行う
        result = {
            "order_id": order_id,
            "action": action,
            "status": "completed",
            "timestamp": datetime.now().isoformat()
        }
        
        # 成功をログに記録
        logger.info(f"注文処理成功: order_id={order_id}")
        
        return result
        
    except Exception as e:
        # エラーをログに記録
        logger.error(f"注文処理エラー: order_id={order_id}, error={str(e)}")
        return {"error": "注文処理中にエラーが発生しました"}
```

## 練習問題

### 問題1: 在庫管理ツールの作成

以下の機能を持つ在庫管理ツールを作成してください：

1. `check_stock(product_id: str) -> Dict`: 在庫数を確認
2. `update_stock(product_id: str, quantity: int) -> Dict`: 在庫を更新
3. `low_stock_alert(threshold: int = 10) -> List[Dict]`: 在庫が少ない商品をリスト

### 解答例

```python
# inventory_tools.py
import json
import os
from typing import Dict, List

INVENTORY_FILE = "inventory.json"

def _load_inventory() -> Dict:
    if os.path.exists(INVENTORY_FILE):
        with open(INVENTORY_FILE, 'r', encoding='utf-8') as f:
            return json.load(f)
    return {}

def _save_inventory(inventory: Dict):
    with open(INVENTORY_FILE, 'w', encoding='utf-8') as f:
        json.dump(inventory, f, ensure_ascii=False, indent=2)

def check_stock(product_id: str) -> Dict:
    """
    商品の在庫数を確認する
    
    Args:
        product_id (str): 商品ID
    
    Returns:
        Dict: 在庫情報
    """
    inventory = _load_inventory()
    
    if product_id in inventory:
        stock_info = inventory[product_id]
        return {
            "product_id": product_id,
            "product_name": stock_info.get("name", "不明"),
            "current_stock": stock_info.get("quantity", 0),
            "unit": stock_info.get("unit", "個")
        }
    else:
        return {"error": f"商品ID {product_id} は登録されていません"}

def update_stock(product_id: str, quantity_change: int) -> Dict:
    """
    在庫数を更新する
    
    Args:
        product_id (str): 商品ID
        quantity_change (int): 在庫の変更数（正: 入荷、負: 出荷）
    
    Returns:
        Dict: 更新結果
    """
    inventory = _load_inventory()
    
    if product_id not in inventory:
        return {"error": f"商品ID {product_id} は登録されていません"}
    
    current_stock = inventory[product_id].get("quantity", 0)
    new_stock = current_stock + quantity_change
    
    if new_stock < 0:
        return {"error": "在庫が不足しています", "current_stock": current_stock, "requested": -quantity_change}
    
    inventory[product_id]["quantity"] = new_stock
    _save_inventory(inventory)
    
    return {
        "product_id": product_id,
        "product_name": inventory[product_id].get("name", "不明"),
        "previous_stock": current_stock,
        "change": quantity_change,
        "new_stock": new_stock
    }

def low_stock_alert(threshold: int = 10) -> List[Dict]:
    """
    在庫が少ない商品をリストアップ
    
    Args:
        threshold (int): 警告する在庫数の閾値
    
    Returns:
        List[Dict]: 在庫が少ない商品のリスト
    """
    inventory = _load_inventory()
    low_stock_items = []
    
    for product_id, info in inventory.items():
        quantity = info.get("quantity", 0)
        if quantity <= threshold:
            low_stock_items.append({
                "product_id": product_id,
                "product_name": info.get("name", "不明"),
                "current_stock": quantity,
                "unit": info.get("unit", "個")
            })
    
    return sorted(low_stock_items, key=lambda x: x["current_stock"])
```

## 宿題

### 課題1: 予約管理システムのツール開発

以下の機能を持つ予約管理ツールを開発してください：

1. **予約作成**: 日時、サービス、顧客情報を登録
2. **予約確認**: 特定の日の予約一覧を表示
3. **予約変更**: 既存の予約を変更
4. **予約キャンセル**: 予約を取り消し
5. **空き時間検索**: 指定期間の空き時間を検索

要件：
- 重複予約の防止
- 営業時間外の予約を拒否
- キャンセル料の計算機能

### 課題2: ツールのテストコード作成

作成したツールに対するテストコードを書いてください：

```python
# test_tools.py
import unittest
from calculator_tools import calculate_tax, calculate_discount

class TestCalculatorTools(unittest.TestCase):
    def test_calculate_tax(self):
        # テストケースを記述
        pass
    
    def test_calculate_discount(self):
        # テストケースを記述
        pass

if __name__ == "__main__":
    unittest.main()
```

### 課題3: COBOLプログラマー向けツール開発ガイド

COBOLプログラマーがPythonでツールを開発する際の注意点やヒントをまとめたガイドを作成してください。以下の項目を含めること：

1. COBOLとPythonの構文対応表
2. よくある間違いと解決方法
3. デバッグのコツ
4. パフォーマンスの考慮点

## まとめ

ツール開発の基本を学びました！

重要なポイント：
- ツールは単純な関数として実装
- 型ヒントとdocstringは必須
- エラー処理を適切に行う
- 戻り値は辞書形式が扱いやすい

COBOLのサブプログラムと同じように、ツールは再利用可能な部品です。良いツールを作ることで、エージェントの能力が大きく向上します。

次は、複数のエージェントを連携させる方法を学びます！