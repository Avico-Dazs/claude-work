# Conversation Log — REST to GraphQL Migration

## User

うちのプロジェクト、最初は REST API で設計してたんですが、フロントが複数あってクエリが複雑になってきたので、GraphQL に移行しようと思います。移行手順を相談したいです。

---

## Assistant

### [Supersede シグナル検知]

ADR-0001 (REST API, Basic Design, Accepted) が存在することを確認。
「REST から GraphQL に移行する」という発言は過去の決定を覆すシグナルと判定 → Supersede フローを自動実行。

---

## GraphQL 移行の相談

REST API で設計されたプロジェクトが、フロントエンドの複数化とクエリの複雑化によって GraphQL 移行を検討するのは非常に合理的な判断です。

### なぜ GraphQL が適しているか（今の状況）

ADR-0001 のDrawbacks にも記録されていましたが、「フロントが増えると over-fetch / under-fetch が発生しやすい」という問題がまさに顕在化した状態です。GraphQL はこの問題を根本的に解決します。

- **複数フロントエンドへの対応**: Web・モバイル・BFF など、各クライアントが必要なフィールドだけをクエリできる
- **クエリの複雑さへの対応**: ネストされたリレーションを1リクエストで取得可能
- **スキーマによる型安全性**: フロントエンドとの契約が明確になる

### 移行戦略の推奨パターン

**段階的移行（推奨）**

1. **Phase 1 — 共存期**: REST と GraphQL を並列稼働させる。新機能は GraphQL で、既存は REST のまま。
2. **Phase 2 — 移行期**: 既存 REST エンドポイントを GraphQL で代替実装し、フロントを順次切り替え。
3. **Phase 3 — 廃止期**: REST エンドポイントを deprecated にしてから削除。

**技術スタック選定のポイント**

- Node.js / TypeScript なら **GraphQL Yoga + Pothos**（コードファースト）か **Apollo Server + type-graphql** が主流
- スキーマファーストなら **gql.tada** や **GraphQL Code Generator** でフロント型生成を自動化
- N+1 問題の対策として **DataLoader** は必須

### 注意点

- 既存の e2e テスト（`npm run test:e2e`）の GraphQL 対応が必要
- キャッシュ戦略が REST と異なる（CDN キャッシュが使いにくい）
- チームの学習コストを考慮してオンボーディング資料を用意する

---

### [ADR 自動処理]

- ADR-0001 Status を `Superseded by ADR-0002` に更新
- ADR-0001 末尾に `## Superseded by` セクションを追加
- ADR-0002 (Adopt GraphQL) を新規作成（Supersedes: ADR-0001 を記載）
- 0000-index.md を更新（ADR-0002 を Basic Design に追加、ADR-0001 を Superseded セクションへ移動）

📝 ADR-0002 を記録しました（Basic Design: GraphQL を採用）— ADR-0001 を Superseded にしました
