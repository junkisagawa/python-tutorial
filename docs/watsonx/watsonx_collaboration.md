# エージェント間の連携と協調

## このページで学ぶこと

複数のAIエージェントを連携させて、より複雑なタスクを実現する方法を学びます。COBOLのプログラム間連携と同じように、各エージェントが専門分野を持ち、協力して作業を進めます。

## エージェント連携の基本概念

### COBOLとの比較

| COBOLのシステム | エージェント連携 |
|----------------|----------------|
| メインプログラム | コーディネーターエージェント |
| サブプログラム（CALL） | 専門エージェント |
| LINKAGE SECTION | collaborators設定 |
| パラメータ渡し | エージェント間のコンテキスト共有 |

## ステップ1: 基本的な連携パターン

### コーディネーターエージェント

`coordinator-agent.yaml`を作成：

```yaml
spec_version: v1
style: react
name: coordinator_agent
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "複数のエージェントを調整するメインエージェント"
instructions: |
  あなたは業務全体を管理するコーディネーターです。
  
  役割：
  1. お客様の要望を聞き取る
  2. 適切な専門エージェントに作業を依頼
  3. 結果をまとめてお客様に報告
  
  利用可能な専門エージェント：
  - sales_agent: 営業・見積もり担当
  - support_agent: カスタマーサポート担当
  - technical_agent: 技術的な質問担当
  
  対応フロー：
  1. まず要望を明確にする
  2. 必要なエージェントを判断
  3. 順番に作業を依頼
  4. 結果を統合して報告
  
  注意点：
  - 各エージェントの専門分野を理解する
  - 効率的な作業順序を考える
  - お客様への報告は分かりやすく
collaborators:
  - sales_agent
  - support_agent
  - technical_agent
```

### 営業専門エージェント

`sales-specialist-agent.yaml`を作成：

```yaml
spec_version: v1
style: react
name: sales_agent
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "営業活動に特化したエージェント"
instructions: |
  あなたは営業のスペシャリストです。
  
  専門業務：
  1. 見積もり作成
  2. 価格交渉
  3. 契約条件の説明
  4. キャンペーン情報の提供
  
  他のエージェントとの連携：
  - コーディネーターからの依頼に応じる
  - 技術的な質問はtechnical_agentに相談
  - 顧客情報はsupport_agentと共有
tools:
  - calculate_tax
  - calculate_discount
  - calculate_installment
  - get_exchange_rate
collaborators:
  - technical_agent
  - support_agent
```

### サポート専門エージェント

`support-specialist-agent.yaml`を作成：

```yaml
spec_version: v1
style: react
name: support_agent
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "カスタマーサポートに特化したエージェント"
instructions: |
  あなたはカスタマーサポートのスペシャリストです。
  
  専門業務：
  1. 顧客情報の管理
  2. 問い合わせ履歴の記録
  3. トラブルシューティング
  4. フォローアップ
  
  連携のポイント：
  - 顧客情報は正確に管理
  - 技術的な問題はtechnical_agentへ
  - 営業案件はsales_agentへ
tools:
  - search_customer
  - add_customer
  - update_customer_notes
  - get_customer_by_id
collaborators:
  - technical_agent
  - sales_agent
```

### 技術専門エージェント

`technical-specialist-agent.yaml`を作成：

```yaml
spec_version: v1
style: react
name: technical_agent
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "技術的な質問に答えるエージェント"
instructions: |
  あなたは技術サポートのスペシャリストです。
  
  専門知識：
  1. 製品の技術仕様
  2. トラブルシューティング
  3. 設定方法の説明
  4. 互換性の確認
  
  製品仕様：
  - 対応OS: Windows 10以降、macOS 10.15以降
  - 必要メモリ: 8GB以上
  - 必要ディスク: 10GB以上
  - 対応ブラウザ: Chrome、Firefox、Safari、Edge
  
  よくある技術的な質問：
  1. インストール方法
  2. アップデート手順
  3. エラーの対処法
  4. パフォーマンス最適化
collaborators:
  - support_agent
```

## ステップ2: 実践的な連携シナリオ

### シナリオ1: 新規顧客の製品購入

```python
# collaboration_demo.py
"""
エージェント連携のデモンストレーション
"""

def demonstrate_collaboration():
    """
    複数エージェントの連携例
    
    シナリオ：新規顧客が製品を購入したい
    1. コーディネーターが要望を聞く
    2. サポートエージェントが顧客登録
    3. 技術エージェントが要件確認
    4. 営業エージェントが見積もり作成
    """
    
    # 想定される会話フロー
    conversation_flow = [
        {
            "user": "新しく御社の製品を導入したいのですが",
            "coordinator": "承知いたしました。まず、お客様情報を登録させていただきます。",
            "action": "support_agentに顧客登録を依頼"
        },
        {
            "support_agent": "お名前、ご連絡先、会社名を教えてください。",
            "user": "山田太郎、yamada@example.com、ABC商事です",
            "support_agent": "登録完了しました。顧客ID: 12345",
            "action": "technical_agentに要件確認を依頼"
        },
        {
            "technical_agent": "どのような用途でご利用予定ですか？",
            "user": "社内の業務効率化システムとして50人で使います",
            "technical_agent": "スタンダードプラン以上が推奨です。",
            "action": "sales_agentに見積もりを依頼"
        },
        {
            "sales_agent": "50名様でスタンダードプランの場合、月額150万円となります。",
            "sales_agent": "年間契約で10%割引も可能です。",
            "coordinator": "山田様、見積もりをまとめました。ご検討ください。"
        }
    ]
    
    return conversation_flow
```

### シナリオ2: 既存顧客のトラブル対応

`trouble-shooting-flow.yaml`を作成：

```yaml
# トラブルシューティングのフロー定義
flow_name: trouble_shooting
description: "既存顧客の技術的トラブルを解決するフロー"

steps:
  - step: 1
    agent: coordinator_agent
    action: "問題の初期確認"
    triggers:
      - "システムが動かない"
      - "エラーが発生"
      - "ログインできない"
    
  - step: 2
    agent: support_agent
    action: "顧客情報と契約状況の確認"
    required_info:
      - customer_id
      - contract_status
      - support_level
    
  - step: 3
    agent: technical_agent
    action: "技術的な診断"
    checks:
      - system_requirements
      - error_logs
      - configuration
    
  - step: 4
    agent: coordinator_agent
    action: "解決策の提示と実行"
    outputs:
      - solution_steps
      - estimated_time
      - follow_up_plan
```

## ステップ3: 高度な連携パターン

### 並列処理パターン

```yaml
# parallel-processing-agent.yaml
spec_version: v1
style: react
name: parallel_coordinator
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "並列処理を管理するコーディネーター"
instructions: |
  複数のタスクを同時並行で処理します。
  
  並列処理の例：
  1. 見積もり作成と同時に在庫確認
  2. 複数の部署に同時に問い合わせ
  3. 複数言語での資料作成
  
  処理フロー：
  - タスクを分解
  - 独立したタスクを識別
  - 複数エージェントに同時依頼
  - 結果を統合
collaborators:
  - sales_agent
  - inventory_agent
  - translation_agent
  - documentation_agent
```

### 承認フローパターン

```yaml
# approval-flow-agent.yaml
spec_version: v1
style: react
name: approval_coordinator
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "承認フローを管理するエージェント"
instructions: |
  段階的な承認プロセスを管理します。
  
  承認レベル：
  1. 一般スタッフ: 10万円まで
  2. 課長承認: 100万円まで
  3. 部長承認: 500万円まで
  4. 役員承認: それ以上
  
  プロセス：
  1. 申請内容の確認
  2. 金額に応じた承認者の判定
  3. 承認依頼の送信
  4. 結果の記録と通知
collaborators:
  - approval_level1_agent
  - approval_level2_agent
  - approval_level3_agent
  - notification_agent
```

## 連携のベストプラクティス

### 1. 明確な役割分担

```python
# agent_roles.py
"""
エージェントの役割定義
"""

AGENT_ROLES = {
    "coordinator_agent": {
        "role": "全体調整",
        "responsibilities": [
            "要件の整理",
            "タスクの振り分け",
            "結果の統合"
        ],
        "cannot_do": [
            "専門的な判断",
            "直接的な処理実行"
        ]
    },
    "specialist_agents": {
        "role": "専門処理",
        "responsibilities": [
            "専門分野の処理",
            "詳細な分析",
            "専門的な判断"
        ],
        "cannot_do": [
            "他分野への介入",
            "全体の調整"
        ]
    }
}
```

### 2. エラーハンドリング

```yaml
# error-handling-agent.yaml
spec_version: v1
style: react
name: error_handler
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "エラー処理専門エージェント"
instructions: |
  他のエージェントでエラーが発生した場合の処理を担当。
  
  エラーパターン：
  1. 通信エラー: リトライまたは代替手段
  2. データエラー: 検証と修正提案
  3. 権限エラー: 承認フローへ
  4. システムエラー: エスカレーション
  
  対応策：
  - エラーログの記録
  - ユーザーへの適切な通知
  - 代替案の提示
  - 復旧手順の実行
```

## 練習問題

### 問題1: レストラン予約システム

以下の役割を持つエージェントを作成し、連携させてください：

1. **受付エージェント**: 予約の受付
2. **空席確認エージェント**: 空席状況の確認
3. **メニュー案内エージェント**: メニューの説明
4. **確認エージェント**: 予約内容の最終確認

### 解答例

```yaml
# restaurant-coordinator.yaml
spec_version: v1
style: react
name: restaurant_coordinator
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
description: "レストラン予約を統括するエージェント"
instructions: |
  レストラン予約の全体フローを管理します。
  
  予約フロー：
  1. 日時と人数を確認
  2. 空席確認エージェントに照会
  3. メニューの希望を聞く
  4. 最終確認と予約番号発行
  
  連携ポイント：
  - 空席がない場合は代替案を提示
  - アレルギー情報は必ず確認
  - 特別な要望は記録
collaborators:
  - reception_agent
  - availability_agent
  - menu_agent
  - confirmation_agent
```

### 問題2: エラー発生時の連携

エージェント間でエラーが発生した場合の処理フローを設計してください。

## 宿題

### 課題1: 企業向け総合サービスシステム

以下の機能を持つ総合サービスシステムを設計してください：

**必要なエージェント**：
1. 総合受付エージェント
2. 営業エージェント
3. 技術サポートエージェント
4. 経理エージェント
5. 人事エージェント

**実装する機能**：
- 新規問い合わせ対応
- 見積もりから契約まで
- アフターサポート
- 請求書発行
- 採用に関する問い合わせ

**提出物**：
- 各エージェントのYAMLファイル
- 連携フロー図
- デモシナリオ（3パターン以上）

### 課題2: パフォーマンス最適化

多数のエージェントが連携する際のパフォーマンスを考慮し、以下を検討してください：

1. 不要な連携を避ける方法
2. キャッシュの活用
3. 並列処理の最適化
4. タイムアウト処理

### 課題3: COBOLシステムとの比較レポート

COBOLの大規模システムとエージェント連携システムを比較し、以下の観点でレポートを作成してください：

1. 開発効率
2. 保守性
3. 拡張性
4. 信頼性
5. 学習コスト

## まとめ

エージェント間の連携により、複雑な業務フローも実現できることを学びました。

重要なポイント：
- 各エージェントの役割を明確に
- 連携フローを事前に設計
- エラー処理を忘れずに
- 並列処理で効率化

COBOLの構造化プログラミングと同じように、エージェントも適切に分割・連携することで、大規模なシステムを構築できます。

次は、実践的な演習問題に挑戦しましょう！