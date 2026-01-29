# Completeness Checklist

定期的に実行して、すべてのツール/エージェント/スキルが適切に統合され、抜けがないことを確認する。

## エージェント完全性チェック

### コア開発エージェント

- [x] **planner** - 実装計画
- [x] **architect** - システム設計
- [x] **tdd-guide** - テスト駆動開発
- [x] **code-reviewer** - コードレビュー
- [x] **security-reviewer** - セキュリティ分析
- [x] **build-error-resolver** - ビルドエラー修正

### クリーンアップ・検証エージェント

- [x] **refactor-cleaner** - デッドコード削除
- [x] **requirements-validator** - 要件準拠検証（新規追加）
- [x] **leakage-detector** - データ漏洩検出（競馬特化）

### テスト・品質保証エージェント

- [x] **e2e-runner** - E2Eテスト
- [x] **python-tester** - Python Pytest
- [x] **python-linter** - Python リンティング

### 言語特化レビューエージェント

- [x] **python-reviewer** - Python コードレビュー
- [x] **go-reviewer** - Go コードレビュー
- [x] **go-build-resolver** - Go ビルド修正

### ドメイン特化エージェント

- [x] **keiba-coder** - 競馬開発
- [x] **keiba-checker** - 競馬データ検証
- [x] **datarobot-specialist** - DataRobot AutoML専門家（新規追加）

### インフラ・ドキュメント

- [x] **database-reviewer** - データベースレビュー
- [x] **doc-updater** - ドキュメント更新

## スキル完全性チェック

### 開発ワークフロー

- [x] **tdd-workflow** - TDDワークフロー
- [x] **coding-standards** - コーディング規約
- [x] **plan** - 実装計画（/plan）

### 言語・フレームワーク特化

- [x] **python-patterns** - Python パターン
- [x] **python-testing** - Python テスト
- [x] **golang-patterns** - Go パターン
- [x] **golang-testing** - Go テスト
- [x] **frontend-patterns** - フロントエンド
- [x] **backend-patterns** - バックエンド

### データベース・分析

- [x] **postgres-patterns** - PostgreSQL
- [x] **clickhouse-io** - ClickHouse

### セキュリティ・品質

- [x] **security-review** - セキュリティレビュー
- [x] **requirements-audit** - 要件監査（新規追加）
- [x] **code-review** - コードレビュー（/code-review）

### テスト関連

- [x] **tdd** - TDD（/tdd）
- [x] **python-test** - Python テスト（/python-test）
- [x] **go-test** - Go テスト（/go-test）
- [x] **e2e** - E2Eテスト（/e2e）
- [x] **test-coverage** - テストカバレッジ（/test-coverage）

### ビルド・修正

- [x] **build-fix** - ビルド修正（/build-fix）
- [x] **go-build** - Go ビルド（/go-build）
- [x] **python-lint** - Python リント（/python-lint）

### クリーンアップ

- [x] **refactor-clean** - リファクタリング（/refactor-clean）

### レビュー

- [x] **python-review** - Python レビュー（/python-review）
- [x] **go-review** - Go レビュー（/go-review）

### ドメイン特化（競馬）

- [x] **keiba-domain** - 競馬ドメイン知識
- [x] **keiba-code** - 競馬コーディング（/keiba-code）
- [x] **keiba-check** - 競馬データ検証（/keiba-check）
- [x] **leakage-check** - 漏洩チェック（/leakage-check）

### ML/AutoMLプラットフォーム

- [x] **datarobot-patterns** - DataRobot SDK パターン（新規追加）
- [x] **datarobot** - DataRobot開発（/datarobot）（新規追加）

### ドキュメント・メタ

- [x] **update-docs** - ドキュメント更新（/update-docs）
- [x] **update-codemaps** - コードマップ更新（/update-codemaps）
- [x] **verify** - 検証（/verify）
- [x] **checkpoint** - チェックポイント（/checkpoint）
- [x] **orchestrate** - オーケストレーション（/orchestrate）
- [x] **eval-harness** - 評価フレームワーク
- [x] **eval** - 評価（/eval）

### 学習・パターン抽出

- [x] **continuous-learning** - 継続学習
- [x] **continuous-learning-v2** - 継続学習v2（本能システム）
- [x] **learn** - 学習（/learn）
- [x] **skill-create** - スキル作成（/skill-create）
- [x] **instinct-status** - 本能ステータス（/instinct-status）
- [x] **instinct-export** - 本能エクスポート（/instinct-export）
- [x] **instinct-import** - 本能インポート（/instinct-import）
- [x] **evolve** - 進化（/evolve）

### その他

- [x] **iterative-retrieval** - 反復検索パターン
- [x] **strategic-compact** - 戦略的コンパクション
- [x] **verification-loop** - 検証ループ（履歴から削除された可能性）

## ワークフロー統合チェック

### 新機能開発フロー

```markdown
1. [x] 要件定義 - /requirements または手動作成
2. [x] 設計 - /design または architect エージェント
3. [x] タスク分解 - /tasks または planner エージェント
4. [x] TDD実装 - tdd-guide エージェント
5. [x] コードレビュー - code-reviewer エージェント
6. [x] セキュリティチェック - security-reviewer エージェント
7. [x] 要件準拠検証 - requirements-validator エージェント（新規）
8. [x] ビルド検証 - build-error-resolver エージェント
9. [x] E2Eテスト - e2e-runner エージェント
```

### クリーンアップフロー

```markdown
1. [x] 要件監査 - /requirements-audit スキル（新規）
2. [x] 要件検証 - requirements-validator エージェント（新規）
3. [x] デッドコード検出 - refactor-cleaner エージェント
4. [x] 削除実行 - refactor-cleaner エージェント
5. [x] テスト検証 - tdd-guide または e2e-runner
6. [x] ビルド検証 - build-error-resolver
```

### 競馬プロジェクト特化フロー

```markdown
1. [x] ドメイン知識 - keiba-domain スキル
2. [x] データ検証 - keiba-checker エージェント
3. [x] 漏洩チェック - leakage-detector エージェント
4. [x] 実装 - keiba-coder エージェント（Kaggle的テクニック）
5. [x] テスト - tdd-guide + leakage-check

Note: DataRobotは必要に応じて利用可能だが、必須ではない
```

## カバレッジギャップ分析

### 現在カバーされている領域

✅ **計画・設計**
- 実装計画（planner）
- アーキテクチャ設計（architect）
- 要件定義（/requirements）

✅ **実装・開発**
- TDD（tdd-guide, tdd-workflow）
- 言語特化（Python, Go, TypeScript/JavaScript）
- ドメイン特化（競馬）

✅ **品質保証**
- コードレビュー（code-reviewer, python-reviewer, go-reviewer）
- セキュリティ（security-reviewer, security-review）
- テスト（e2e-runner, python-tester, 各種テストスキル）

✅ **クリーンアップ・保守**
- デッドコード削除（refactor-cleaner）
- **要件準拠検証（requirements-validator）← 新規追加**
- リファクタリング支援

✅ **ドキュメント**
- ドキュメント更新（doc-updater）
- コードマップ更新（/update-codemaps）

### 潜在的ギャップ（今後検討すべき領域）

⚠️ **パフォーマンス最適化**
- 専用のパフォーマンスレビューエージェントなし
- 対処法: code-reviewer で部分的にカバー、または performance-reviewer エージェント作成検討

⚠️ **依存関係管理**
- 依存関係の更新・セキュリティ監査専用ツールなし
- 対処法: refactor-cleaner で部分的にカバー（depcheck）、または dependency-auditor 作成検討

⚠️ **API設計レビュー**
- REST/GraphQL API 設計専用レビュアーなし
- 対処法: architect + code-reviewer で対応可能、または api-reviewer 作成検討

⚠️ **アクセシビリティ（a11y）**
- フロントエンドのアクセシビリティ専用チェックなし
- 対処法: frontend-patterns でガイダンス提供、または a11y-checker 作成検討

⚠️ **国際化（i18n）**
- 多言語対応専用ツールなし
- 対処法: coding-standards でカバー、必要に応じて i18n-helper 作成

✅ **カバレッジ十分（追加不要）**
- データベース（database-reviewer, postgres-patterns, clickhouse-io）
- セキュリティ（security-reviewer, security-review）
- 要件管理（requirements-validator, requirements-audit）← 新規追加で完全カバー

## 統合性検証

### agents.md との整合性

- [x] すべてのエージェントが agents.md に記載されている
- [x] 新規エージェント requirements-validator が追加されている
- [x] 使用タイミングが明記されている

### スキルとエージェントの対応

| スキル | 呼び出すエージェント | 整合性 |
|--------|---------------------|--------|
| /requirements-audit | requirements-validator | ✅ |
| /refactor-clean | refactor-cleaner | ✅ |
| /code-review | code-reviewer | ✅ |
| /security-review | security-reviewer | ✅ |
| /tdd | tdd-guide | ✅ |
| /e2e | e2e-runner | ✅ |
| /build-fix | build-error-resolver | ✅ |
| /python-review | python-reviewer | ✅ |
| /python-lint | python-linter | ✅ |
| /python-test | python-tester | ✅ |
| /go-review | go-reviewer | ✅ |
| /go-build | go-build-resolver | ✅ |
| /keiba-code | keiba-coder | ✅ |
| /keiba-check | keiba-checker | ✅ |
| /leakage-check | leakage-detector | ✅ |
| /datarobot | datarobot-specialist | ✅ |
| /update-docs | doc-updater | ✅ |

### ワークフロー網羅性

- [x] 開発開始から本番デプロイまでのフルフロー対応
- [x] 各プログラミング言語（Python, Go, TypeScript/JavaScript）サポート
- [x] ドメイン特化（競馬）サポート
- [x] **要件準拠とクリーンアップのギャップ解消** ← 今回の追加で達成

## 推奨される次のアクション

### 優先度: 高

1. ✅ **要件準拠検証ツールの追加** ← 完了
   - requirements-validator エージェント作成
   - /requirements-audit スキル作成

### 優先度: 中

2. ⚠️ **パフォーマンスレビューエージェント検討**
   - 必要性: プロジェクト次第
   - 代替: code-reviewer で部分対応可能

3. ⚠️ **依存関係セキュリティ監査強化**
   - 現状: refactor-cleaner の depcheck で部分対応
   - 改善案: security-reviewer に統合、または専用エージェント

### 優先度: 低

4. ⚠️ **API設計レビューエージェント**
   - 必要性: API中心のプロジェクトで有用
   - 代替: architect + code-reviewer で対応可能

5. ⚠️ **アクセシビリティチェッカー**
   - 必要性: フロントエンド重視プロジェクトで有用
   - 代替: frontend-patterns でガイダンス提供

## 定期メンテナンス

### 月次チェック

- [ ] 新しいエージェント/スキルが追加されたか確認
- [ ] agents.md が最新か確認
- [ ] ワークフロー統合が適切か検証
- [ ] 未使用のエージェント/スキルがないか確認

### 四半期チェック

- [ ] カバレッジギャップの再評価
- [ ] 新技術・新ツールへの対応検討
- [ ] プロジェクト特化エージェント/スキルの棚卸し
- [ ] ドキュメントの整合性確認

---

**結論**:
- ✅ requirements-validator エージェントと /requirements-audit スキルの追加により、要件準拠検証のギャップが解消された
- ✅ 開発フローにおける「デッドコード削除」と「要件外コード削除」の両面がカバーされた
- ✅ 現時点でコア機能は完全にカバーされている
- ⚠️ パフォーマンス、依存関係セキュリティ、API設計、a11yは必要に応じて追加検討
