# ADR-0001: Adopt Next.js App Router

| Field | Value |
|-------|-------|
| **ID** | ADR-0001 |
| **Stage** | Basic Design |
| **Status** | Accepted |
| **Date** | 2026-03-22 |
| **Supersedes** | — |

---

## Context

Next.js 13+ では Pages Router と App Router の2つのルーティング方式が提供されている。
新規プロジェクトのルーティング戦略を決定する必要がある。
App Router は React Server Components を活用したデータフェッチの簡素化、
レイアウトのネスト、ストリーミングレンダリングをサポートしており、
Next.js チームの推奨方式でもある。
一方で Pages Router はエコシステムの成熟度が高く、
既存のドキュメントや知見も豊富である。

## Decision

Next.js App Router を採用する。
`/app` ディレクトリを起点とし、Server Components をデフォルトとする。
Pages Router (`/pages`) は使用しない。

## Verification (Harness)

```
npm run test:arch
```

アーキテクチャテストで `/pages` ディレクトリへの参照が存在しないこと、
およびすべての新規ルートが `/app` 配下にあることを検証する。

## Consequences

### Benefits（メリット）

- React Server Components によりサーバーサイドのデータフェッチが簡潔になる
- レイアウトのネスト（Nested Layouts）でコードの再利用性が向上する
- ストリーミングレンダリングにより初期表示が高速化する
- Next.js チームの長期サポート対象であり、将来の機能拡張が期待できる

### Drawbacks（デメリット・トレードオフ）

- 学習コストが高い（Server Components と Client Components の使い分けが必要）
- 一部のサードパーティライブラリが App Router に未対応の場合がある
- Pages Router に比べてコミュニティの事例・Q&A がまだ少ない

### Side Effects（副作用・注意点）

- `use client` ディレクティブの付け忘れによるバグに注意
- ミドルウェアは引き続き `/middleware.ts` に配置する（変更なし）
