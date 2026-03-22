---
name: doc-test-spec
description: >
  Requirement・Detailed Design ステージの ADR から「システムテスト仕様書（E2E テスト仕様）」を生成します。
  V字モデルの頂点（Requirement と対応）と末端（Detailed Design と対応）のテストケースを統合し、
  /docs/deliverables/test/system-test-spec.md に出力します。
  トリガー: doc:test-spec, テスト仕様書を生成, E2Eテスト仕様, システムテスト仕様書, テスト仕様
---

# doc:test-spec — システムテスト仕様書生成

## 目的

V字モデルの両端（Requirement の E2E テストと Detailed Design の単体テスト）に対応する
テスト仕様書を生成します。要件の受入テストから実装パターンの単体テストまで、
テスト工程全体の仕様を一元管理できる形で出力します。

**このスキルは `/docs/deliverables/` にのみ書き込みます。`/docs/adr/` は変更しません。**

---

## 実行手順

### ステップ1：ADR を読み込む

1. `/docs/adr/0000-index.md` を読み込む
2. `## Requirement` セクションと `## Detailed Design` セクションの Accepted ADR をすべて読み込む
3. 各 ADR から以下を抽出する：
   - `Verification (Harness)` → テストコマンド・検証説明
   - `Decision` → テスト対象機能・検証すべき振る舞い
   - `Context` → テストの目的・背景
   - `Consequences > Benefits` → テストで証明すべき価値
   - `Consequences > Drawbacks` → テストで検出できないリスク

### ステップ2：ドキュメントを生成する

`/docs/deliverables/test/system-test-spec.md` を以下の構造で生成します。

```markdown
# システムテスト仕様書

> 本書は ADR から自動生成されています。
> 原典: /docs/adr/ | 生成日: {今日の日付}
> V字モデル対応: Requirement（E2E）/ Detailed Design（単体）

---

## 1. テスト目的・スコープ

**目的:** {Requirement ADR の Benefits を統合してテストの存在意義を記述}

**スコープ:**
- E2E テスト: {Requirement ADR の数}件
- 単体テスト: {Detailed Design ADR の数}件
- 除外: {Drawbacks から識別された非カバー領域}

---

## 2. テストケース一覧

| TC-ID | 種別 | 対象 ADR | テスト名 | 検証コマンド |
|-------|------|---------|---------|------------|
| TC-{NNNN}-E2E | E2E | ADR-NNNN | {テスト名} | `{Verification}` |
| TC-{NNNN}-UNIT | 単体 | ADR-NNNN | {テスト名} | `{Verification}` |
...

---

## 3. テストケース詳細

### TC-{NNNN}-E2E: {ADR タイトル}（E2E）

**対象 ADR:** ADR-NNNN
**テスト種別:** E2E（システムテスト）
**検証コマンド:** `{Verification コマンド}`

**前提条件:**
{Decision が前提とするシステム状態・データ}

**テスト観点:**
{Consequences > Benefits から逆算した確認ポイント}

**期待結果:**
{Decision に記載された振る舞いが実現されていること}
{具体的な数値・条件がある場合は明記（例: 1%のポイント付与）}

**テスト困難な領域:**
{Consequences > Drawbacks}

---

### TC-{NNNN}-UNIT: {ADR タイトル}（単体）

**対象 ADR:** ADR-NNNN
**テスト種別:** 単体テスト
**検証コマンド:** `{Verification コマンド}`

**テスト対象:**
{Context から特定されるクラス・関数・モジュール}

**テスト観点:**
- 正常系: {Decision の主ケース}
- 異常系: {Drawbacks から推論できる境界・エラーケース}

**強制ルール（Linter）:**
{Side Effects の ESLint ルール等}

---

## 4. 要件トレーサビリティマトリクス

{Requirement ADR と Detailed Design ADR の対応関係を表形式で示す}

| 要件 ADR | 機能概要 | 対応テストケース | 実装 ADR |
|---------|---------|----------------|---------|
| ADR-NNNN | {Decision 要約} | TC-NNNN-E2E | ADR-MMMM |
...

---

## 5. テストカバレッジ

\```mermaid
flowchart TD
  subgraph Requirements["要件（E2E テスト）"]
    {Requirement ADR をノードとして列挙}
  end
  subgraph Implementation["実装（単体テスト）"]
    {Detailed Design ADR をノードとして列挙}
  end
  {Requirement と Detailed Design の対応を矢印で表現}
\```

---

## 6. 非カバー領域・リスク

| リスク | 内容 | 対応方針 |
|--------|------|---------|
{全 ADR の Drawbacks から非カバー領域を集約}

---

## 7. 変更履歴

{Superseded ADR を列挙}
```

### ステップ3：完了を通知する

```
📄 システムテスト仕様書を生成しました
  出力先: /docs/deliverables/test/system-test-spec.md
  E2E テストケース: {N}件（Requirement ADR）
  単体テストケース: {N}件（Detailed Design ADR）
```

---

## 注意事項

- ADR が Requirement・Detailed Design の両方とも 0 件の場合は「対象 ADR がまだ記録されていません」と出力する
- Verification コマンドが同一（例: `npm run test:e2e`）の ADR が複数あっても、テストケースは ADR ごとに別々に記載する
- 要件トレーサビリティは ADR 同士の明示的なリンクがない場合、Decision の文脈から推論して記載する
