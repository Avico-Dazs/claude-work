# ADR-0002: Migrate to Zod for Input Validation

| Field | Value |
|-------|-------|
| **ID** | ADR-0002 |
| **Stage** | Detailed Design |
| **Status** | Accepted |
| **Date** | 2026-03-22 |
| **Supersedes** | [ADR-0001: Use class-validator](0001-use-class-validator.md) |

---

## Context

ADR-0001 で採用した class-validator は TypeScript の型と乖離しやすく、
スキーマと型定義を二重管理するコストが大きい。
Zod はスキーマから TypeScript 型を自動推論でき、フロントエンドとの共有も容易なため移行を検討した。

## Decision

`class-validator` / `class-transformer` を `zod` に移行する。
すべてのバリデーションスキーマを `zod.object()` で定義し、
`z.infer<>` で型を導出する。NestJS では `ZodValidationPipe` を使用する。

## Verification (Harness)

```
npm run test:unit
```

## Consequences

### Benefits（メリット）
- スキーマと型定義を一元管理できる（`z.infer` による型推論）
- フロントエンドとスキーマを共有できる（isomorphic）
- リフレクションメタデータが不要になり `tsconfig` が簡潔になる

### Drawbacks（デメリット・トレードオフ）
- 既存 DTO の書き換えコストがかかる（移行期間が必要）
- NestJS の標準 ValidationPipe が使えなくなる

### Side Effects（副作用・注意点）
- `class-transformer` の `plainToInstance` を使っている箇所も要修正
