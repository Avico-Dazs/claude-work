# 会話ログ — ADR Supersede 承認フロー

> シミュレーション日時: 2026-03-22
> スキル: adr-manager
> 操作: ADR-0001 (REST API) → ADR-0002 (GraphQL) への Supersede

---

## Turn 1 — ユーザー

> うちのプロジェクト、最初は REST API で設計してたんですが、フロントが複数あってクエリが複雑になってきたので、GraphQL に移行しようと思います。移行手順を相談したいです。

---

## Turn 2 — Claude（ADR-0001 読み込み・Supersede 提案）

なるほど、複数フロントエンドによるクエリ複雑化ですね。それは GraphQL に移行する典型的な動機です。

既存の ADR を確認しました。**ADR-0001「REST API の採用」**（Basic Design / Accepted）が記録されています。今回の移行はこれを Supersede する変更になります。

### GraphQL 移行の進め方

**1. 移行戦略の選択**

| アプローチ | 特徴 | 推奨シーン |
|-----------|------|-----------|
| **段階的移行（ストラングラーフィグ）** | REST と GraphQL を並行稼働し、フロントエンドを順次移行 | 本番稼働中のシステム |
| **一括切り替え** | 一度に全て移行 | 初期フェーズ・小規模 |

本番稼働中なら**段階的移行**を強く推奨します。

**2. 推奨技術スタック**

- サーバー側: Apollo Server（Node.js）/ Strawberry（Python）/ gqlgen（Go）
- クライアント側: Apollo Client / urql / TanStack Query + graphql-request
- N+1 対策: DataLoader

**3. 移行ステップ概要**

1. GraphQL スキーマの設計（既存 REST リソースを型に変換）
2. サーバー側に GraphQL エンドポイントを追加（`/graphql`）
3. 既存 REST エンドポイントはそのまま維持
4. フロントエンドを1つずつ GraphQL クライアントに移行
5. 全フロントエンド移行後、REST エンドポイントを廃止

**4. 注意点**

- HTTP キャッシュが効きにくい → Persisted Queries や CDN の工夫が必要
- 認証・認可の設計を GraphQL のリゾルバ単位で見直す
- スキーマファーストで設計し、`graphql-inspector` で整合性を継続検証する

---

この移行方針が固まりましたので、ADR として記録しておくことを提案します。

---
📋 **ADR 変更提案（Supersede）**

以下の変更を適用してよろしいですか？

**変更① ADR-0001 のステータス変更**
- Status: Accepted → Superseded by ADR-0002

**変更② 新規 ADR-0002 を作成**
- Stage: Basic Design
- Decision: バックエンド API を REST から GraphQL に移行し、クライアントが必要なデータを単一クエリで柔軟に取得できるアーキテクチャに切り替える。
- Verification: `npm run test:graphql` / `npx graphql-inspector validate ./schema.graphql`
- Supersedes: ADR-0001

承認する場合は「OK」、修正がある場合は内容を教えてください。

---

---

## Turn 3 — ユーザー

> OK、それで進めてください

---

## Turn 4 — Claude（Supersede 実行・確定通知）

承認ありがとうございます。Supersede を実行します。

以下の変更を適用しました：

**変更①** `/docs/adr/0001-adopt-rest-api.md` を更新
- Status: `Accepted` → `Superseded by ADR-0002`
- `## Superseded by` セクションを末尾に追記

**変更②** `/docs/adr/0002-migrate-to-graphql.md` を新規作成
- Stage: Basic Design
- Status: Accepted
- Supersedes: ADR-0001

**変更③** `/docs/adr/0000-index.md` を更新
- ADR-0001 を「Superseded / Deprecated」セクションへ移動
- ADR-0002 を「Basic Design」セクションに追加

---

📝 **ADR-0002 を確定しました（Basic Design: GraphQL API への移行）**

| 操作 | ファイル | 内容 |
|------|---------|------|
| 更新 | `0001-adopt-rest-api.md` | Status → Superseded by ADR-0002 |
| 作成 | `0002-migrate-to-graphql.md` | GraphQL 採用の新規 ADR |
| 更新 | `0000-index.md` | インデックス更新 |

次のステップとして、GraphQL スキーマの設計や具体的な移行計画が固まったら、Detailed Design の ADR も追記していきましょう。また、移行完了後に REST 廃止のタイミングを ADR として記録することも推奨します。
