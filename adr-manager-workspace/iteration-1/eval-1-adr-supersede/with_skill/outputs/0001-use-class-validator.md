# ADR-0001: Use class-validator for Input Validation

| Field | Value |
|-------|-------|
| **ID** | ADR-0001 |
| **Stage** | Detailed Design |
| **Status** | Superseded by ADR-0002 |
| **Date** | 2026-01-10 |
| **Supersedes** | — |

---

## Context

バックエンド API の入力バリデーション方式を統一する必要がある。
NestJS との親和性を重視し class-validator を採用した。

## Decision

`class-validator` と `class-transformer` を使用してすべての DTO をデコレータで検証する。

## Verification (Harness)

```
npm run test:unit
```

## Consequences

### Benefits（メリット）
- NestJS の ValidationPipe と統合が容易
- デコレータベースで宣言的に記述できる

### Drawbacks（デメリット・トレードオフ）
- ランタイムのリフレクションメタデータが必要（`tsconfig` 設定が必要）
- スキーマの型推論が弱い（TypeScript の型と乖離しやすい）

---

## Superseded by

[ADR-0002: Migrate to Zod for Input Validation](0002-migrate-to-zod.md) — 2026-03-22
