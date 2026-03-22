# ADR-0001: REST API の採用

| Field | Value |
|-------|-------|
| **ID** | ADR-0001 |
| **Stage** | Basic Design |
| **Status** | Superseded by ADR-0002 |
| **Date** | 2026-01-15 |
| **Supersedes** | — |

---

## Context

プロジェクト開始時、バックエンドとフロントエンドの通信インターフェースとして REST API を採用した。
シンプルなリソース指向の設計で、開発チームの習熟度も高く、標準的な選択肢として合意された。

## Decision

バックエンド API として REST（HTTP + JSON）を採用し、エンドポイントをリソース単位で設計する。

## Verification (Harness)

```
npm run test:api
```

## Consequences

### Benefits（メリット）

- 標準的なアーキテクチャで学習コストが低い
- HTTP キャッシュ・CDN との親和性が高い
- ツールサポートが豊富（Swagger / OpenAPI）

### Drawbacks（デメリット・トレードオフ）

- フロントエンドが複数になるとオーバーフェッチ・アンダーフェッチが発生しやすい
- 複雑なクエリにはエンドポイントの増殖を招く

### Side Effects（副作用・注意点）

- API バージョニング戦略が別途必要

---

## Superseded by

[ADR-0002: GraphQL API への移行](0002-migrate-to-graphql.md) — 2026-03-22
