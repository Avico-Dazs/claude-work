---
name: doc-requirements
description: >
  Requirement ステージの ADR から「要件定義書」を生成します。
  RDRA3.0 フォーマット（システムコンテキスト・ビジネスコンテキスト・ユースケース・情報モデル・状態モデル・要件一覧）で構造化し、
  各要件は EARS 記法（WHEN/WHILE/IF-THEN/WHERE/The）で記述します。
  /docs/deliverables/requirements/requirements-spec.md に出力します。
  トリガー: doc:requirements, 要件定義書を生成, 要件定義書を作って, 要件定義書が欲しい,
  RDRA, EARS記法, 要件定義
---

# doc:requirements — RDRA3.0 + EARS 記法による要件定義書生成

## 目的

`/docs/adr/` に蓄積された Requirement ステージの ADR を読み込み、
**RDRA3.0** フォーマット（関係駆動要件分析）で構造化した要件定義書を生成します。
各要件文は **EARS 記法**（Easy Approach to Requirements Syntax）で記述します。

**このスキルは `/docs/deliverables/` にのみ書き込みます。`/docs/adr/` は変更しません。**

---

## EARS 記法ガイド

ADR の `Decision` フィールドを以下のパターンに変換してください。

| パターン | 構文 | 変換例 |
|---------|------|-------|
| **Ubiquitous**（常時） | `The [system] shall [response]` | 「ポイント台帳に記録する」→ `The loyalty system shall record a ledger entry for each point transaction.` |
| **Event-driven**（イベント） | `WHEN [trigger] the [system] shall [response]` | 「購入完了時にポイントを付与」→ `WHEN a purchase is confirmed, the loyalty system shall award points equal to 1% of the purchase amount.` |
| **State-driven**（状態） | `WHILE [state] the [system] shall [response]` | 「有効期限内のポイントを残高に含める」→ `WHILE points are within their 1-year validity period, the loyalty system shall include them in the available balance.` |
| **Unwanted behavior**（異常） | `IF [trigger] THEN the [system] shall [response]` | 「100pt未満は利用不可」→ `IF the available balance is below 100 points, THEN the loyalty system shall reject the redemption request.` |
| **Optional feature**（条件付き） | `WHERE [feature is included], the [system] shall [response]` | 特定の機能オプションが有効な場合のみ適用される要件 |

**変換の判断基準：**
- 数値・閾値・制限が含まれる → Unwanted behavior (`IF...THEN`)
- 「〜時に」「〜したとき」が含まれる → Event-driven (`WHEN`)
- 「〜の間」「〜の状態で」が含まれる → State-driven (`WHILE`)
- 常に成立するルール → Ubiquitous (`The ... shall`)

---

## RDRA3.0 文書構造

### ステップ1：ADR を読み込む

1. `/docs/adr/0000-index.md` を読み込む
2. `## Requirement` セクションの `Status: Accepted` な ADR をすべて読み込む
3. 各 ADR から以下を抽出する：
   - `Context` → システムの背景・課題・ビジネスゴール
   - `Decision` → 要件の主文（EARS に変換する）
   - `Verification (Harness)` → 受入条件コマンド
   - `Consequences > Benefits` → ビジネス価値・期待効果
   - `Consequences > Drawbacks` → 制約・前提条件・スコープ外

### ステップ2：RDRA3.0 フォーマットで文書を生成する

`/docs/deliverables/requirements/requirements-spec.md` を以下の構造で生成します。

---

```markdown
# 要件定義書

> **フォーマット:** RDRA3.0 | **要件記法:** EARS (Easy Approach to Requirements Syntax)
> 本書は ADR（アーキテクチャ決定記録）から自動生成されています。
> 原典: /docs/adr/ | 生成日: {今日の日付}

---

## 目次

1. システムコンテキスト
2. ビジネスコンテキスト
3. ユースケース
4. 情報モデル
5. 状態モデル
6. 要件一覧（EARS）
7. 変更履歴

---

## 1. システムコンテキスト

{Context フィールドを横断してシステムの目的とスコープを記述する}

### 1.1 システムコンテキスト図

アクター（外部ユーザー・外部システム）とシステムの関係を図示する。
ADR の Context・Decision から登場する主体（名詞）を抽出してアクターを推論する。

\```mermaid
flowchart LR
  subgraph System["[システム名]"]
    direction TB
    UC1[{主要ユースケース1}]
    UC2[{主要ユースケース2}]
  end
  Actor1(["{アクター1}"]) --> System
  Actor2(["{アクター2}"]) --> System
  System --> ExternalSystem(["{外部システム（あれば）}"])
\```

### 1.2 アクター一覧

| アクター | 種別 | 説明 |
|---------|------|------|
| {名前} | 人間 / 外部システム | {ADR Context から推論した役割} |

---

## 2. ビジネスコンテキスト

### 2.1 背景と目的

{各 ADR の Context を統合してビジネス上の課題・ゴールを記述する}

### 2.2 ビジネスルール

{Decision に含まれる数値・条件・期限をビジネスルールとして抽出する}

| BR-ID | ルール | 根拠 ADR |
|-------|--------|---------|
| BR-001 | {具体的な数値・条件ルール} | ADR-NNNN |
...

### 2.3 利用シーン（Usage Scenarios）

{Context から読み取れる、ユーザーがシステムを使う典型的な状況を記述する}

---

## 3. ユースケース

### 3.1 ユースケース図

\```mermaid
flowchart TD
  Actor(["{主要アクター}"])
  subgraph System["システム境界"]
    {各 Decision から読み取れるユースケースを列挙}
    UC1["{ユースケース名}"]
    UC2["{ユースケース名}"]
  end
  Actor --> UC1
  Actor --> UC2
\```

### 3.2 システムユースケース詳細

{各 Requirement ADR から1つ以上のユースケースを展開する}

#### UC-{N}: {ユースケース名}

| 項目 | 内容 |
|------|------|
| **アクター** | {Decision から推論した主体} |
| **事前条件** | {Drawbacks または Decision の条件節から推論} |
| **基本フロー** | {Decision の主文を手順に分解} |
| **代替フロー・例外** | {Drawbacks または IF 条件から推論} |
| **事後条件** | {Benefits から推論した達成状態} |
| **受入コマンド** | `{Verification Harness}` |

**フロー図:**
\```mermaid
sequenceDiagram
  actor {アクター名}
  participant {システム名}
  {Decision から読み取れる手順をシーケンスとして表現}
  {IF 条件がある場合は alt/else ブロックで表現}
\```

---

## 4. 情報モデル

{Decision に登場する名詞・エンティティを抽出して関係を示す}
{詳細な ER 図は doc:domain を参照}

### 4.1 主要情報（エンティティ）一覧

| 情報名 | 説明 | 主な属性 | 関連 ADR |
|--------|------|---------|---------|
| {名詞} | {Decision から読み取れる意味} | {数値・日付など Decision に含まれる属性} | ADR-NNNN |
...

---

## 5. 状態モデル

{Decision に「〜の間」「〜状態で」などの状態表現が含まれる場合に生成する}
{該当する記述がない場合は「本要件定義の範囲では状態モデルは不要」と記載する}

\```mermaid
stateDiagram-v2
  [*] --> {初期状態}
  {状態} --> {次の状態} : {遷移条件（WHEN/IF の内容）}
  {状態} --> [*] : {終了条件}
\```

---

## 6. 要件一覧（EARS 記法）

各要件は EARS 記法で記述する。パターンは Ubiquitous / Event / State / Unwanted / Optional から選ぶ。

| REQ-ID | EARS パターン | 要件文 | 種別 | 優先度 | 関連 UC | 根拠 ADR | 検証 |
|--------|-------------|--------|------|--------|--------|---------|------|
| REQ-001 | {Event/State/Unwanted/Ubiquitous/Optional} | {EARS 形式の要件文（英語または日本語）} | 機能/非機能 | Must/Should/Could | UC-{N} | ADR-NNNN | `{コマンド}` |
...

**優先度の定義（MoSCoW）:**
- **Must**: システムが成立するために必須
- **Should**: 強く推奨されるが、初期リリース時は省略可能
- **Could**: あれば望ましい
- **Won't**: 今回のスコープ外（将来の候補）

**非機能要件:**
{Consequences > Drawbacks から非機能要件を抽出してテーブルに追加する}

---

## 7. 変更履歴

| ADR | 変更内容 | 置換先 | 日付 |
|-----|---------|--------|------|
{Superseded 状態の ADR を列挙。なければ「変更履歴なし」}
```

---

### ステップ3：完了を通知する

```
📄 要件定義書（RDRA3.0 / EARS）を生成しました
  出力先: /docs/deliverables/requirements/requirements-spec.md
  対象 ADR: ADR-NNNN, ...
  生成した要件数（EARS）: {N} 件
```

---

## 注意事項

- 要件文は **できる限り EARS 記法で記述する**。どのパターンにも当てはまらない場合のみ自然文で補足する
- RDRA3.0 では「情報」と「状態」は ADR から推論する。記述が不足している場合は「推論に基づく（要確認）」と注記する
- `/docs/deliverables/` は CLAUDE.md に含めないこと（AI コンテキスト汚染を防ぐ）
- Superseded な ADR は本文に含めず変更履歴セクションのみに記載する
- ADR が 0 件の場合は「Requirement ステージの ADR がまだ記録されていません」と出力する
