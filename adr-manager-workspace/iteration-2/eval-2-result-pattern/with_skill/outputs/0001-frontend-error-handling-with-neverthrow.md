# ADR-0001: フロントエンドエラーハンドリングに neverthrow（Result 型）を採用する

| Field | Value |
|-------|-------|
| **ID** | ADR-0001 |
| **Stage** | Detailed Design |
| **Status** | Accepted |
| **Date** | 2026-03-22 |
| **Supersedes** | — |

---

## Context

フロントエンドコードベース全体に `try-catch` が散在しており、以下の問題が発生していた。

- エラーの見落とし・握りつぶしが起きやすく、障害時のデバッグが困難
- 関数の戻り値の型にエラーが現れないため、呼び出し元がエラーを意識しにくい
- エラーパスのテストカバレッジが不明確になっていた

これらを解消するため、エラーハンドリングを型安全かつ統一的なパターンに移行することを検討した。

## Decision

フロントエンドのエラーハンドリングパターンを `neverthrow` ライブラリの `Result` / `ResultAsync` 型で統一する。新規コードはすべて `Result<T, E>` を返すように実装し、既存コードは境界部分から段階的に置き換える。`try-catch` の直接使用はアダプター層（外部ライブラリのラップ）に限定する。

## Verification (Harness)

```
npm run test:unit
```

ユニットテストにて各関数の成功パス・失敗パスの両方を `Result` 型として検証する。加えて、ESLint カスタムルールまたは `no-restricted-syntax` で `try-catch` の直接使用を警告するよう設定することを推奨する:

```
npx eslint --rule '{"no-restricted-syntax": ["warn", {"selector": "TryStatement", "message": "Use neverthrow Result instead of try-catch"}]}'
```

## Consequences

### Benefits（メリット）

- TypeScript の型システムにより、エラーを処理していないコードがコンパイルエラーになる
- `map` / `andThen` / `orElse` のチェーンで宣言的かつ追跡しやすいエラーフローを記述できる
- ユニットテストでエラーパスを自然に網羅できる
- API コールやバリデーションが多いフロントエンドと特に相性が良い

### Drawbacks（デメリット・トレードオフ）

- 既存コードのリライトコストが発生する（段階的移行で緩和可能）
- `async/await` との組み合わせは `ResultAsync` を介する必要があり、初見では若干冗長に見える
- 関数型スタイルに不慣れなメンバーへの学習コストがかかる

### Side Effects（副作用・注意点）

- サードパーティライブラリが例外をスローする箇所はアダプター関数でラップして `Result` に変換すること
- `ResultAsync` はPromiseを返すため、既存の `async/await` コードとの混在には注意が必要
- 段階的移行期間中は `try-catch` と `Result` が混在するため、境界を明示するコーディング規約を設けることを推奨する

---
