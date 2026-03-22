# adr:check — Verification Guardrail Status

| ADR | Stage | Verification | Result | Detail |
|-----|-------|-------------|--------|--------|
| ADR-0001 | Requirement | `npm run test:e2e` | ✅ OK | `package.json` に `test:e2e` スクリプトが定義されています |
| ADR-0002 | Detailed Design | `npx oxlint` | ✅ OK | `oxlint` (v1.56.0) が利用可能です |
| ADR-0003 | Basic Design | `archgate.config.ts` | ❌ ERROR | `/tmp/adr-check-test/archgate.config.ts` が存在しません — `npm install archgate` と設定ファイルの作成が必要です |

---

**サマリー:** 2/3 OK, 0 WARNING, 1 ERROR

> ⚠️ ADR-0003 のガードレールが機能していません。`archgate.config.ts` を作成してレイヤー依存ルールを設定してください。
