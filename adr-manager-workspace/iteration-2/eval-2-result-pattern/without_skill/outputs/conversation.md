# 会話サマリー: フロントエンドエラーハンドリング方針の検討

**日付:** 2026-03-22
**テーマ:** try-catch から neverthrow (Result 型パターン) への移行検討

---

## Turn 1

**User:** フロントエンドのエラーハンドリングをどうするか悩んでいます。今は try-catch を散在させているんですが、Result 型パターン（neverthrow ライブラリ）を使って統一したいと思っています。どう思いますか？

**Assistant:** 良いアプローチだと伝えた上で、以下を整理した。

- **try-catch の問題点:** エラーが関数シグネチャから見えない、catch 漏れが起きやすい、async/await との組み合わせでさらに問題が増える。
- **neverthrow のメリット:** `Result<T, E>` 型で成功/失敗が型レベルで明示される、`.match()` / `.andThen()` でエラー処理を強制できる、TypeScript の exhaustive check と連携できる。
- **デメリット・注意点:** チームの学習コスト、既存コードへの段階的移行の必要性、サードパーティライブラリとの境界でラッパー層が必要。
- チームの TypeScript 習熟度と既存コードベース規模を確認した。

---

## Turn 2

**User (simulated):** チームは TypeScript に慣れています。中規模コードベースで、API 呼び出しと状態管理周りから先に移行したい。neverthrow で進める場合の注意点は？

**Assistant:** 段階的移行を肯定した上で、以下の 4 点を案内した。

1. **境界ラッパーを最初に作る** — `ResultAsync.fromPromise()` で fetch/axios をラップし、try-catch を一か所に集約する。
2. **エラー型を先に設計する** — `E` を `unknown` にしない。`ApiError`、`ValidationError` など、ドメインごとのエラー型を先に定義する。
3. **既存コードとの混在期間を想定する** — `fromThrowable` / `fromPromise` で throw ベースコードを Result に変換しながら、境界を少しずつ広げていく。
4. **`.andThen()` チェーンの長さに注意** — `combine` や do-notation 風ヘルパーを活用する場面を想定しておく。

---

## Turn 3

**User (simulated):** わかりました、neverthrow で進めることにします。

**Assistant:** 移行方針を支持した。また、「なぜ neverthrow を採用したか」「どう移行するか」を ADR として残すことを提案したが、ユーザーからの明示的な依頼がなかったため、ADR ファイルの作成は行わなかった。

---

## 結論

- ユーザーは **neverthrow を採用** することを決定した。
- 移行スコープ: API 呼び出しと状態管理周りを優先。
- ADR 作成については提案したが、このセッションでは作成しなかった（明示的な依頼なし）。

---

## 作成ファイル

| ファイル | 説明 |
|---|---|
| `outputs/conversation.md` | 本ファイル。会話サマリー。 |

（ADR ファイルは作成していない）
