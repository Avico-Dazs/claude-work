# ADR-0001: Prisma を ORM として採用する

| Field | Value |
|-------|-------|
| **ID** | ADR-0001 |
| **Stage** | Basic Design |
| **Status** | Accepted |
| **Date** | 2026-03-22 |
| **Supersedes** | — |

---

## Context

新規 Node.js バックエンドプロジェクトの立ち上げにあたり、ORM の選定が必要になった。
候補は Prisma と TypeORM の2つ。プロジェクトの非機能要件として型安全性を最重要視しており、
コンパイル時に型の不一致を検出できることが求められる。

TypeORM はデコレータベースのアプローチで型安全性が部分的にしか保証されず、
ランタイムエラーのリスクが残る。また、TypeORM のメンテナンス状況に対する懸念もある。

## Decision

ORM として **Prisma** を採用する。`schema.prisma` によるスキーマ定義を唯一の真実 (Single Source of Truth) とし、
自動生成されるクライアント型によってコンパイル時の型安全性を確保する。

## Verification (Harness)

```
npx tsc --noEmit
npx prisma validate
```

## Consequences

### Benefits（メリット）

- `schema.prisma` からクライアントコードが自動生成され、DB スキーマと TypeScript 型が常に同期される
- コンパイル時に型の不一致を検出でき、ランタイムエラーを未然に防げる
- `prisma migrate` による安全なマイグレーション管理
- `$queryRaw` を使った Raw SQL も型安全に記述できる
- ドキュメントが充実しており、エコシステムが活発に開発中

### Drawbacks（デメリット・トレードオフ）

- Prisma クライアントのコード生成ステップが必要（CI/CD に `prisma generate` を追加する必要あり）
- ActiveRecord パターンは使えない（Data Mapper パターンのみ）
- 複雑な継承マッピング (Table Inheritance) のサポートが TypeORM より限定的

### Side Effects（副作用・注意点）

- `prisma generate` を `postinstall` スクリプトに追加することを推奨
- 将来的に TypeORM ベースのライブラリ（例: NestJS の一部プラグイン）と統合する場合は調整が必要
- スキーマ変更時は必ず `prisma migrate dev` を実行すること

---
