# ADR-0001: ORM に Prisma を採用する

| Field | Value |
|-------|-------|
| **ID** | ADR-0001 |
| **Stage** | Basic Design |
| **Status** | Accepted |
| **Date** | 2026-03-22 |
| **Supersedes** | — |

---

## Context

新しい Node.js バックエンドプロジェクトを開始するにあたり、ORM の選定が必要になった。
候補は Prisma と TypeORM の2択で、プロジェクトとして型安全性を重視する方針がある。
TypeORM はデコレータベースのアプローチで実績があるが、型推論の精度に限界がある。
Prisma はスキーマファーストで Prisma Client の型が自動生成されるため、
ランタイムエラーをコンパイル時に検出できる強い型安全性を持つ。

## Decision

Node.js バックエンドの ORM として Prisma を採用する。

## Verification (Harness)

```
npx prisma validate && npx tsc --noEmit
```

## Consequences

### Benefits（メリット）

- Prisma Client の型が自動生成され、クエリ結果の型が完全に推論される
- `prisma validate` でスキーマの整合性を CI で機械的に検証できる
- マイグレーション管理が `prisma migrate` で一元化され、履歴が明確になる
- Prisma Studio による GUI でのデータ確認が開発効率を高める

### Drawbacks（デメリット・トレードオフ）

- スキーマ変更のたびに `prisma generate` が必要で、ビルドステップが増える
- 複雑な生 SQL やストアドプロシージャとの相性が TypeORM より劣る場合がある
- TypeORM に比べてデコレータベースの既存コードとの統合が難しい

### Side Effects（副作用・注意点）

- `prisma/schema.prisma` をスキーマの単一ソース・オブ・トゥルースとして管理する
- CI パイプラインに `prisma validate` と `tsc --noEmit` を追加する必要がある
- チームメンバーは Prisma の学習コストを考慮すること

---
