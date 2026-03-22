---
name: doc-all
description: >
  すべての V字モデル成果物ドキュメントを一括生成します。
  doc:domain → doc:requirements → doc:architecture → doc:class →
  doc:test-spec → doc:test-plan の順に実行し、
  /docs/deliverables/INDEX.md を生成して全成果物を一覧化します。
  トリガー: doc:all, 全ドキュメントを生成, V字モデル成果物を全部作って, 成果物をすべて生成
---

# doc:all — V字モデル成果物 一括生成

## 目的

V字モデルのすべてのフェーズに対応する成果物ドキュメントを一括生成します。
各ドキュメントは `/docs/adr/` の ADR を正規表現として自動生成されます。

**このスキルは `/docs/deliverables/` にのみ書き込みます。`/docs/adr/` は変更しません。**

---

## 実行手順

### ステップ1：ADR の全体把握

1. `/docs/adr/0000-index.md` を読み込む
2. 各ステージ（Requirement / Basic Design / Detailed Design）の ADR 数を確認する
3. ユーザーに以下を報告してから生成を開始する：

```
📋 生成対象を確認しました
  Requirement ADR: {N}件 → doc:requirements, doc:domain, doc:test-spec (E2E)
  Basic Design ADR: {N}件 → doc:architecture, doc:test-plan
  Detailed Design ADR: {N}件 → doc:class, doc:test-spec (単体)

V字モデル成果物を生成します...
```

### ステップ2：V字モデル左辺（定義フェーズ）を生成

以下の順番で生成します（上流から下流へ）：

1. **概念モデル** (`doc:domain` の手順に従う)
   - 出力: `/docs/deliverables/requirements/domain-model.md`
   - `✅ 概念モデル 生成完了` と通知

2. **要件定義書** (`doc:requirements` の手順に従う)
   - 出力: `/docs/deliverables/requirements/requirements-spec.md`
   - `✅ 要件定義書 生成完了` と通知

3. **アーキテクチャ設計書** (`doc:architecture` の手順に従う)
   - 出力: `/docs/deliverables/basic-design/architecture.md`
   - `✅ アーキテクチャ設計書 生成完了` と通知

4. **クラス図・実装パターン設計書** (`doc:class` の手順に従う)
   - 出力: `/docs/deliverables/detailed-design/class-diagram.md`
   - `✅ クラス図・実装パターン設計書 生成完了` と通知

### ステップ3：V字モデル右辺（検証フェーズ）を生成

5. **システムテスト仕様書** (`doc:test-spec` の手順に従う)
   - 出力: `/docs/deliverables/test/system-test-spec.md`
   - `✅ システムテスト仕様書 生成完了` と通知

6. **結合テスト計画書** (`doc:test-plan` の手順に従う)
   - 出力: `/docs/deliverables/test/integration-test-plan.md`
   - `✅ 結合テスト計画書 生成完了` と通知

### ステップ4：成果物インデックスを生成

`/docs/deliverables/INDEX.md` を以下の形式で生成します：

```markdown
# V字モデル成果物インデックス

> 本書は ADR から自動生成されています。
> 原典: /docs/adr/ | 生成日: {今日の日付}

---

## V字モデル構成図

\```mermaid
flowchart LR
  subgraph Left["左辺（定義フェーズ）"]
    REQ["📄 要件定義書\n/requirements/requirements-spec.md"]
    DOM["📄 概念モデル\n/requirements/domain-model.md"]
    ARCH["📄 アーキテクチャ設計書\n/basic-design/architecture.md"]
    CLS["📄 クラス図\n/detailed-design/class-diagram.md"]
  end
  subgraph Right["右辺（検証フェーズ）"]
    TSPEC["📄 システムテスト仕様書\n/test/system-test-spec.md"]
    TPLAN["📄 結合テスト計画書\n/test/integration-test-plan.md"]
  end
  REQ <-->|"対応"| TSPEC
  ARCH <-->|"対応"| TPLAN
\```

---

## 成果物一覧

### 要件フェーズ

| ドキュメント | ファイル | 対応 ADR | 最終更新 |
|------------|--------|---------|---------|
| 要件定義書 | [requirements-spec.md](requirements/requirements-spec.md) | {Requirement ADR ID リスト} | {今日の日付} |
| 概念モデル | [domain-model.md](requirements/domain-model.md) | {Requirement ADR ID リスト} | {今日の日付} |

### 基本設計フェーズ

| ドキュメント | ファイル | 対応 ADR | 最終更新 |
|------------|--------|---------|---------|
| アーキテクチャ設計書 | [architecture.md](basic-design/architecture.md) | {Basic Design ADR ID リスト} | {今日の日付} |

### 詳細設計フェーズ

| ドキュメント | ファイル | 対応 ADR | 最終更新 |
|------------|--------|---------|---------|
| クラス図・実装パターン | [class-diagram.md](detailed-design/class-diagram.md) | {Detailed Design ADR ID リスト} | {今日の日付} |

### テストフェーズ

| ドキュメント | ファイル | 対応 ADR | 最終更新 |
|------------|--------|---------|---------|
| システムテスト仕様書 | [system-test-spec.md](test/system-test-spec.md) | {Requirement + Detailed ADR ID リスト} | {今日の日付} |
| 結合テスト計画書 | [integration-test-plan.md](test/integration-test-plan.md) | {Basic Design ADR ID リスト} | {今日の日付} |

---

## 生成元 ADR サマリ

| ADR | タイトル | Stage | 成果物への反映 |
|-----|---------|-------|--------------|
{0000-index.md の全 Accepted ADR を列挙し、どのドキュメントに反映されたかを記載}
```

### ステップ5：完了を通知する

```
🎉 V字モデル成果物をすべて生成しました

  /docs/deliverables/
  ├── INDEX.md
  ├── requirements/
  │   ├── requirements-spec.md
  │   └── domain-model.md
  ├── basic-design/
  │   └── architecture.md
  ├── detailed-design/
  │   └── class-diagram.md
  └── test/
      ├── system-test-spec.md
      └── integration-test-plan.md

  生成元 ADR: {全 ADR ID リスト}
```

---

## 個別スキルの利用

成果物を個別に更新したい場合は以下のコマンドを使用してください：

| コマンド | 生成ドキュメント |
|---------|--------------|
| `doc:requirements` | 要件定義書 |
| `doc:domain` | 概念モデル |
| `doc:architecture` | アーキテクチャ設計書 |
| `doc:class` | クラス図・実装パターン設計書 |
| `doc:test-spec` | システムテスト仕様書 |
| `doc:test-plan` | 結合テスト計画書 |

---

## 注意事項

- ADR が 1 件も存在しない場合は「`/docs/adr/` に ADR がまだ記録されていません。adr-manager で決定事項を記録してください。」と出力して終了する
- 特定のステージの ADR が 0 件の場合は、そのステージのドキュメントを「対象 ADR なし」として空ファイルで生成する（インデックスには記載する）
- `/docs/deliverables/` は CLAUDE.md に含めないこと
