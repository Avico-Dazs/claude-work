# ADR-0002: Adopt GraphQL

| Field | Value |
|-------|-------|
| **ID** | ADR-0002 |
| **Stage** | Basic Design |
| **Status** | Accepted |
| **Date** | 2026-03-22 |
| **Supersedes** | [ADR-0001: Adopt REST API](0001-adopt-rest-api.md) |

---

## Context

当初はフロントエンドが1つでシンプルな CRUD 主体だったため、REST API を採用した（ADR-0001）。
しかしその後、フロントエンドが複数（Web・モバイルなど）に増加し、各クライアントが必要とするデータ形状が異なることで
over-fetch / under-fetch が頻発するようになった。
また、リレーションを跨ぐ複雑なクエリが増え、REST のエンドポイント設計では対応が困難になってきた。

## Decision

API 方式を REST から GraphQL に移行する。各クライアントが必要なフィールドだけをクエリできる柔軟なスキーマを提供し、
複数フロントエンドへの対応と複雑なデータ取得要件を解決する。

移行は段階的に行い、REST と GraphQL を一定期間並列稼働させた後、REST エンドポイントを廃止する。

## Verification (Harness)

```
npm run test:e2e
npx graphql-inspector validate schema.graphql
```

## Consequences

### Benefits（メリット）

- 複数フロントエンドが必要なフィールドだけを取得でき、over-fetch / under-fetch を解消できる
- ネストされたリレーションを1リクエストで取得でき、クライアントのクエリ記述が簡潔になる
- スキーマが API の型安全な契約として機能し、フロントエンドの型生成（GraphQL Code Generator 等）が可能
- 新機能追加時にエンドポイントのバージョニングが不要

### Drawbacks（デメリット・トレードオフ）

- REST と比べてチームの学習コストがかかる（クエリ言語・リゾルバー設計・DataLoader パターン等）
- CDN レベルのキャッシュが使いにくい（POST リクエスト主体のため）
- N+1 問題が発生しやすく、DataLoader による対策が必須
- 移行期間中は REST と GraphQL の二重メンテナンスが発生する

### Side Effects（副作用・注意点）

- 既存の `npm run test:e2e` を GraphQL エンドポイントに対応させる必要がある
- REST API クライアント（外部連携など）が存在する場合は別途移行計画が必要
- パフォーマンスモニタリングを GraphQL クエリ単位で見直す必要がある

---
