# ADR-{NNNN}: {Title}

| Field | Value |
|-------|-------|
| **ID** | ADR-{NNNN} |
| **Stage** | {Requirement \| Basic Design \| Detailed Design} |
| **Status** | Proposed <!-- 承認後は Accepted に変更する --> |
| **Date** | {YYYY-MM-DD} |
| **Supersedes** | — |

---

## Context

<!-- なぜこの決定が必要になったか。背景・制約・解決すべき課題を記述する。
     将来の自分や AI エージェントがこの決定の「なぜ」を理解できるように書く。 -->

{Context here}

## Decision

<!-- 何をどうするかを明確に述べる。
     「〜を採用する」「〜を禁止する」のように、判断を一文で言い切ること。 -->

{Decision here}

## Verification (Harness)

<!-- この決定が守られているかを機械的に検証する手段を記載する。
     コマンド例: `npm run test:e2e`, `npx oxlint --rule no-console`, `archgate check`
     検証できない場合は理由を書き、代替の確認手段を検討すること。 -->

```
{verification command or file reference}
```

## Consequences

### Benefits（メリット）

- {benefit 1}

### Drawbacks（デメリット・トレードオフ）

- {drawback 1}

### Side Effects（副作用・注意点）

- {side effect 1}

---

<!-- Superseded になった場合は以下を追記する -->
<!-- ## Superseded by
[ADR-{NNNN}: {New Title}]({NNNN}-{slug}.md) — {Date} -->
