# ADR-0001: Adopt REST API

| Field | Value |
|-------|-------|
| **ID** | ADR-0001 |
| **Stage** | Basic Design |
| **Status** | Superseded by ADR-0002 |
| **Date** | 2026-01-10 |
| **Supersedes** | — |

## Context
新規バックエンドの API 方式を決定する必要があった。
シンプルな CRUD 操作が主体であり、クライアントは Web フロントエンド1つを想定していた。

## Decision
REST API を採用する。リソース設計に従い、HTTP メソッドと URL でエンドポイントを表現する。

## Verification (Harness)
```
npm run test:e2e
```

## Consequences
### Benefits
- シンプルで学習コストが低い
- ツールチェーンが成熟している

### Drawbacks
- フロントが増えると over-fetch / under-fetch が発生しやすい

---

## Superseded by
[ADR-0002: Adopt GraphQL](0002-adopt-graphql.md) — 2026-03-22
