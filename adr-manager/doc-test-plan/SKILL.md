---
name: doc-test-plan
description: >
  Basic Design ステージの ADR から「結合テスト計画書」を生成します。
  V字モデルの右辺（Basic Design と対応する統合テスト）として、
  テスト観点・シナリオ・実行フローを /docs/deliverables/test/integration-test-plan.md に出力します。
  トリガー: doc:test-plan, 結合テスト計画書を生成, 統合テスト計画, 結合テスト
---

# doc:test-plan — 結合テスト計画書生成

## 目的

Basic Design ステージの ADR の `Verification (Harness)` フィールドを中心に、
システム結合レベルのテスト計画を生成します。V字モデルにおいて基本設計と対になる
テストドキュメントです。

**このスキルは `/docs/deliverables/` にのみ書き込みます。`/docs/adr/` は変更しません。**

---

## 実行手順

### ステップ1：ADR を読み込む

1. `/docs/adr/0000-index.md` を読み込む
2. `## Basic Design` セクションの Accepted ADR をすべて読み込む
3. 参照として `## Requirement` セクションの ADR も読み込む（受入条件との対応付け用）
4. 各 Basic Design ADR から以下を抽出する：
   - `Verification (Harness)` → テストコマンド・テストスコープ
   - `Decision` → テスト対象コンポーネント・統合ポイント
   - `Consequences > Drawbacks` → テストが困難なケース・制限事項
   - `Consequences > Side Effects` → CI パイプライン要件

### ステップ2：ドキュメントを生成する

`/docs/deliverables/test/integration-test-plan.md` を以下の構造で生成します。

```markdown
# 結合テスト計画書

> 本書は ADR から自動生成されています。
> 原典: /docs/adr/ | 生成日: {今日の日付}
> V字モデル対応: Basic Design ステージ

---

## 1. テスト方針・スコープ

**テスト種別:** 結合テスト（統合テスト）
**対象ステージ:** Basic Design
**スコープ:** {Decision から読み取れる統合境界（DB連携・サービス間等）を記述}

---

## 2. テスト環境

| 項目 | 内容 |
|------|------|
| 実行コマンド | `{Verification コマンドの共通部分}` |
| DB | {採用 DB（Prisma/PostgreSQL 等）を Decision から推論} |
| 必要なインフラ | {Side Effects・Drawbacks から推論} |

---

## 3. テスト観点マトリクス

| テスト観点 | 対象 ADR | 検証コマンド | 合否基準 |
|-----------|---------|------------|---------|
| {コンポーネント統合の観点} | ADR-NNNN | `{Verification コマンド}` | {Benefits から逆算した合否基準} |
...

---

## 4. テストシナリオ

{各 Basic Design ADR についてシナリオを生成}

### シナリオ {N}: ADR-NNNN {タイトル} の結合検証

**目的:** {Context の課題が解決されているかを検証}

**前提条件:**
- {Decision が前提とする環境・データ状態}

**テスト手順:**
1. {Decision の採用技術に対して統合ポイントを操作する手順}
2. ...

**期待結果:**
- {Consequences > Benefits に記載された効果が確認できること}

**検証コマンド:**
\```
{Verification Harness}
\```

**テスト困難な領域:** {Consequences > Drawbacks}

---

## 5. テスト実行フロー

\```mermaid
flowchart TD
  Setup["環境セットアップ\n(DB起動・マイグレーション)"]
  {各テストシナリオをノードとして列挙}
  Report["テストレポート生成"]
  Setup --> {最初のシナリオ}
  {シナリオ間の依存関係を矢印で表現}
  {最後のシナリオ} --> Report
\```

---

## 6. リスク・除外事項

{Consequences > Drawbacks から特定された制限・テスト不可領域}

| リスク | 内容 | 対応方針 |
|--------|------|---------|
...

---

## 7. 変更履歴

{Superseded ADR を列挙}
```

### ステップ3：完了を通知する

```
📄 結合テスト計画書を生成しました
  出力先: /docs/deliverables/test/integration-test-plan.md
  対象 ADR: ADR-NNNN, ...
  テストシナリオ数: {N}
```

---

## 注意事項

- Basic Design ADR が 0 件の場合は「Basic Design ステージの ADR がまだ記録されていません」と出力する
- `Verification (Harness)` が `npm run test:integration` のような場合は、そのコマンドをシナリオの検証コマンドとして使用する
