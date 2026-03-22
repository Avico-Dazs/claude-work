---
name: doc-domain
description: >
  Requirement ステージの ADR から「概念モデル（ドメインモデル図）」を生成します。
  ビジネスエンティティ・集約・値オブジェクト・ドメインイベントを Mermaid erDiagram で可視化し、
  ユビキタス言語辞書と合わせて /docs/deliverables/requirements/domain-model.md に出力します。
  トリガー: doc:domain, 概念モデルを生成, ドメインモデルを作って, 概念モデル, ドメインモデル
---

# doc:domain — 概念モデル / ドメインモデル生成

## 目的

Requirement ステージの ADR から、ビジネスドメインの構造を可視化した概念モデルを生成します。
DDD（ドメイン駆動設計）の語彙（Entity / Value Object / Aggregate / Domain Event）を使い、
開発チームと非技術者が共通認識を持てるドキュメントを作ります。

**このスキルは `/docs/deliverables/` にのみ書き込みます。`/docs/adr/` は変更しません。**

---

## 実行手順

### ステップ1：ADR からドメイン語彙を抽出する

1. `/docs/adr/0000-index.md` を読み込む
2. `## Requirement` セクションの Accepted ADR をすべて読み込む
3. 各 ADR の `Decision` フィールドから以下を抽出する：

| 抽出対象 | 抽出ルール | DDD 分類 |
|---------|-----------|---------|
| 固有名詞・名詞句 | 「〜ポイント」「〜ユーザー」「〜注文」 | Entity / Value Object 候補 |
| 動詞句 | 「〜を付与する」「〜を利用する」 | Domain Event / Command 候補 |
| 数値・比率・期限 | 「1%」「100pt以上」「1年間」 | Value Object の制約 |
| 条件節 | 「〜以上から」「〜の場合は」 | Business Rule |

### ステップ2：ドキュメントを生成する

`/docs/deliverables/requirements/domain-model.md` を以下の構造で生成します。

```markdown
# 概念モデル（ドメインモデル）

> 本書は ADR から自動生成されています。
> 原典: /docs/adr/ | 生成日: {今日の日付}

---

## 1. ドメイン語彙集（ユビキタス言語）

| 用語 | 定義 | 分類 | 出典 ADR |
|------|------|------|---------|
| {名詞} | {Decision から読み取れる意味・制約} | Entity / Value Object / Event | ADR-NNNN |
...

---

## 2. エンティティ関係図

\```mermaid
erDiagram
  {Decision から抽出したエンティティを ER 図として表現}
  {リレーション（1対多・多対多等）は文脈から推論する}
  {Value Object の属性は エンティティ内に記載}
\```

---

## 3. 集約境界

{関連するエンティティをグループ化して集約ルートを特定する}

| 集約名 | 集約ルート | 内包エンティティ・VO | 整合性ルール |
|--------|-----------|-------------------|------------|
| {集約名} | {ルートエンティティ} | {構成要素} | {Drawbacks / Decision から抽出} |

---

## 4. ドメインイベント一覧

| イベント名 | トリガー | 結果 | 出典 ADR |
|-----------|---------|------|---------|
| {動詞 + 過去形 / 〜された} | {何が起きたとき} | {何が変化する} | ADR-NNNN |
...

---

## 5. ビジネスルール制約

{Decision に含まれる数値・条件・期限を構造化する}

| ルール | 内容 | 検証方法 |
|--------|------|---------|
| {ルール名} | {具体的な条件・閾値} | `{Verification コマンド}` |
...

---

## 6. スコープ外・未解決事項

{Consequences > Drawbacks から将来の課題・未定義領域を記載}

---

## 7. 変更履歴

{Superseded ADR を列挙}
```

### ステップ3：完了を通知する

```
📄 概念モデルを生成しました
  出力先: /docs/deliverables/requirements/domain-model.md
  抽出したエンティティ: {Entity 名リスト}
  抽出したイベント: {Event 名リスト}
```

---

## Mermaid erDiagram 生成ガイド

```
erDiagram
  USER {
    string id PK
    string name
  }
  POINT_BALANCE {
    int balance
    date expiry
  }
  ORDER {
    string id PK
    int totalAmount
    date purchasedAt
  }
  POINT_TRANSACTION {
    int amount
    string type "awarded | used"
    date createdAt
  }
  USER ||--o{ POINT_BALANCE : "has"
  USER ||--o{ ORDER : "places"
  ORDER ||--|| POINT_TRANSACTION : "triggers"
  POINT_BALANCE ||--o{ POINT_TRANSACTION : "records"
```

リレーションの推論ルール：
- 「ユーザーが購入する」→ USER ||--o{ ORDER
- 「購入ごとにポイントを付与」→ ORDER ||--|| POINT_TRANSACTION
- 「ポイントを利用できる」→ USER/POINT_BALANCE → ORDER への依存

---

## 注意事項

- エンティティ名は ADR の日本語をそのまま使うか、英語の技術名詞に統一するかを文脈で判断する
- VO（Value Object）は属性を持つがIDを持たないもの（金額、ポイント数、期限等）
- ADR が 0 件の場合は「Requirement ステージの ADR がまだ記録されていません」と出力する
