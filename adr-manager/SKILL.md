---
name: adr-manager
description: >
  V字モデルの開発工程（Requirement / Basic Design / Detailed Design）に対応したアーキテクチャ決定記録（ADR）を
  管理します。会話の中で決定事項を検知し、人間の承認を得てからファイルに記録します。
  Harness Engineering の「不変性・追跡性・段階的開示・決定論的ガードレール」の原則に基づき、
  設計判断を /docs/adr/ に蓄積します。また adr:export で人間向けの V字モデル成果物ドキュメントを生成します。

  【重要】ユーザーが ADR を意識しなくても、会話の中で以下のシグナルを検知したらこのスキルを起動してください：
  - ビジネス要件・機能仕様が合意された（「〜機能を追加する」「〜の要件は〜にする」「〜を満たす必要がある」）
  - 技術選定・方式決定が合意された（「〜を使う」「〜で行く」「〜に決めた」「〜を採用する」）
  - アーキテクチャ・データモデル・実装パターンについて方針が固まった
  - 過去の決定が覆った（「やっぱり〜に変える」「〜から〜に移行する」「〜をやめて〜にする」）
  - ユーザーが Claude の提案に同意して方向性が確定した
  - `adr:new`, `adr:supersede`, `adr:list`, `adr:check`, `adr:export` を明示的に呼んだ

  決定を検知したら、即座にファイルを作成せず、まず草案を提示して人間の承認を求めてください。
---

# ADR Manager — 会話から設計判断を検知し、承認を得てから記録する

このスキルには2つの役割があります：

1. **AI のための記憶**（`/docs/adr/`）：承認済みの決定事項を構造化して蓄積。
2. **人間のための成果物**（`/docs/deliverables/`）：ADR から V字モデル成果物を生成。AI コンテキストには含めない。

**根本原則：決定事項は書き換えず、新しい決定で古いものを Supersede します。**
**承認原則：ファイルへの書き込みは、必ず人間の承認を得てから行います。**

---

## ディレクトリ設計

```
/docs/
├── adr/                        ← AIコンテキスト（CLAUDE.md から参照）
│   ├── 0000-index.md
│   ├── 0001-*.md
│   └── ...
└── deliverables/               ← 人間向け成果物（AIコンテキスト外）
    ├── requirements/
    │   └── requirements-spec.md
    ├── basic-design/
    │   └── basic-design.md
    └── detailed-design/
        └── detailed-design.md
```

**CLAUDE.md には `/docs/adr/` だけを参照させ、`/docs/deliverables/` は含めない。**

---

## V字モデルの工程（Stage）定義

| Stage | 対象 | 主な検証手段 | 対応成果物 |
|-------|------|-------------|-----------|
| **Requirement** | 機能要件・ビジネスロジック・外部システム契約 | E2E テスト | 要件定義書 |
| **Basic Design** | アーキテクチャ・レイヤー・DB・フレームワーク | 構造テスト・依存関係リンター | 基本設計書 |
| **Detailed Design** | コンポーネント・関数・実装パターン・ライブラリ | 単体テスト・標準リンター | 詳細設計書 |

---

## 承認フロー（最重要）

決定の瞬間を検知したら、**ファイルを作成する前に**必ず草案を提示して承認を求めてください。
ファイルへの書き込みは承認後にのみ行います。

### ステップ1：草案を提示する

会話の回答の末尾に、以下の形式で ADR 草案を提示します：

```
---
📋 **ADR 作成提案**

以下の内容で記録してよろしいですか？

**ADR-{NNNN}: {タイトル}**
- Stage: {Requirement / Basic Design / Detailed Design}
- Decision: {決定内容を一文で}
- Verification: `{検証コマンド}`

承認する場合は「OK」、修正がある場合は内容を教えてください。
---
```

Supersede の場合は変更の全体像を示します：

```
---
📋 **ADR 変更提案（Supersede）**

以下の変更を適用してよろしいですか？

**変更① ADR-{old-id} のステータス変更**
- Status: Accepted → Superseded by ADR-{new-id}

**変更② 新規 ADR-{new-id} を作成**
- Stage: {stage}
- Decision: {決定内容}
- Verification: `{コマンド}`
- Supersedes: ADR-{old-id}

承認する場合は「OK」、修正がある場合は内容を教えてください。
---
```

### ステップ2：承認・差し戻しを待つ

ユーザーの返答を判断します：

| ユーザーの返答 | 判断 | 次のアクション |
|--------------|------|--------------|
| 「OK」「承認」「はい」「その通り」「進めて」など | ✅ 承認 | ファイルを作成し `📝 ADR-NNNN を確定しました` と通知 |
| 具体的な修正内容 | ✏️ 差し戻し | 修正した草案を再提示し、再度承認を求める |
| 「やめて」「不要」「却下」など | ❌ 却下 | ファイルを作成せず「了解です、記録しません」と回答 |

### ステップ3：承認後にファイルを作成する

承認を得たら以下を実行します：

1. `assets/adr-template.md` を参照して ADR ファイルを生成する
2. ファイル名: `{NNNN}-{kebab-case-title}.md`
3. `/docs/adr/0000-index.md` を更新する
4. `adr:lint {filename}` を実行する
   - error あり → auto-fixable は `adr:format` で修正、残るエラーは確定通知前にユーザーへ報告する
   - warning のみ → 確定通知と同時に一覧表示する
5. 完了を通知する：`📝 ADR-{NNNN} を確定しました（{Stage}: {タイトル}）`

Supersede の場合は、旧 ADR の更新と新 ADR の作成を両方行います。

---

## 決定の瞬間を検知する

### 新しい決定

| シグナル | 例 | Stage 推論 |
|---------|-----|-----------|
| ビジネス要件の合意 | 「月次で請求書を自動生成する」「〜機能を追加することになった」 | Requirement |
| 技術・ライブラリの採用合意 | 「Prisma を使おう」「Zod でいきます」 | Basic/Detailed |
| アーキテクチャ方針の確定 | 「レイヤードアーキテクチャにする」 | Basic Design |
| 実装パターンの統一 | 「Result 型で統一する」 | Detailed Design |
| Claude の提案をユーザーが承認 | 「それで行きましょう」「OK、そうします」 | 文脈から推論 |

### 過去の決定の覆し（Supersede）

| シグナル | 例 |
|---------|-----|
| 要件の変更 | 「やっぱり月次ではなく週次にする」 |
| 技術の乗り換え | 「class-validator から Zod に移行する」 |
| 方針転換 | 「REST から GraphQL に切り替える」 |

矛盾を検知するため、`/docs/adr/0000-index.md` が存在する場合は事前に読み込んでください。

---

## ADR テンプレート

`assets/adr-template.md` を参照してください。承認後にファイルを生成するときに使います。

**必須フィールド：**

1. **ID & Title** — `ADR-{NNNN}: {タイトル}`
2. **Stage** — 会話の文脈から推論
3. **Status** — 承認後は `Accepted`（Supersede の場合は旧 ADR が `Superseded by ADR-{new-id}`）
4. **Context** — 会話の背景・課題を要約
5. **Decision** — 何をどうするかを一文で断言する
6. **Verification (Harness)** — 機械的に守るコマンドを推論して記載
   - Requirement → `npm run test:e2e`
   - Basic Design → 構造テスト・依存関係リンター
   - Detailed Design → `npm run test:unit`・標準リンター
7. **Consequences** — メリット・デメリット・副作用

---

## `adr:lint [file|--all]`

ADR ファイルの構造的整合性を **列挙されたルールに従って機械的に** 検証します。
LLM の判断ではなく、以下のルールを上から順に適用します。

**ルール一覧：**

| Code | Severity | Auto-fix | チェック内容 |
|------|----------|----------|------------|
| **L001** | error    | no  | ファイル名が `\d{4}-[a-z0-9-]+\.md` または `0000-index.md` でない |
| **L002** | error    | no  | メタデータ表に必須フィールド欠如（ID / Stage / Status / Date / Supersedes） |
| **L101** | error    | no  | メタデータの ID 値がファイル名の番号と不一致 |
| **L102** | error    | no  | Stage が `Requirement` / `Basic Design` / `Detailed Design` 以外 |
| **L103** | error    | no  | Status が `Proposed` / `Accepted` / `Superseded by ADR-{NNNN}` 以外 |
| **L104** | error    | yes | Date が `YYYY-MM-DD` 形式でない |
| **L105** | error    | no  | Supersedes が `—` または `ADR-{NNNN}` 以外 |
| **L201** | error    | no  | 必須 h2 セクション欠如（Context / Decision / Verification (Harness) / Consequences） |
| **L202** | error    | no  | `## Consequences` 配下に必須 h3 欠如（Benefits / Drawbacks / Side Effects） |
| **L203** | error    | no  | `## Verification (Harness)` にコードブロックがない |
| **L301** | warning  | no  | Requirement ADR の Verification に E2E テスト参照がない（`e2e`, `test:e2e` を期待） |
| **L302** | warning  | no  | Basic Design ADR の Verification に構造・依存テスト参照がない（`integration`, `arch`, `tsc` 等を期待） |
| **L303** | warning  | no  | Detailed Design ADR の Verification に単体テスト・リンター参照がない（`unit`, `lint` 等を期待） |
| **L401** | error    | no  | `0000-index.md` が存在しない |
| **L402** | error    | yes | ADR ファイルがインデックスに未登録 |
| **L403** | error    | no  | インデックスにリンクがあるが対応ファイルが存在しない |
| **L404** | error    | no  | `Superseded by ADR-{NNNN}` で参照している ADR ファイルが存在しない |
| **L405** | warning  | yes | インデックス内の Stage 分類が ADR の Stage フィールドと不一致 |

**注意：** L202 の h3 マッチングは前方一致（`### Benefits（メリット）` も `### Benefits` として認識）。

**出力形式（エラーあり）：**

```
ADR Lint Results
──────────────────────────────────────
docs/adr/0002-adopt-prisma.md
  ✗ L202 [error]   Missing required subsection '### Side Effects'
           WHY: 構造化されたトレードオフ記録がないと doc:* 生成に欠損が生じる
           FIX: '### Side Effects（副作用・注意点）' を追加し、最低1件を記載する

  ⚠ L302 [warning] Basic Design の Verification に構造テスト参照がない
           FIX: 'npx tsc --noEmit' または 'archgate check' をコードブロックに追加する

──────────────────────────────────────
1 error, 1 warning — auto-fixable: 0 (run 'adr:format' to apply)
```

**出力形式（クリーン）：**

```
✓ All 3 ADR files passed (0 errors, 0 warnings)
```

---

## `adr:format [file|--all]`

自動修正可能な lint 違反のみを修正します（L104, L402, L405）。LLM の判断は不要な変換のみ対象です。

- **L104**: Date を `YYYY-MM-DD` に正規化（曖昧な場合はスキップして報告）
- **L402**: インデックスに未登録の ADR を正しい Stage セクションに追記
- **L405**: インデックス内の誤った Stage セクションに配置されたエントリを移動

修正後に `adr:lint` を再実行して残る違反を確認してください。

---

## `adr:export [stage]`

ADR から人間向けの V字モデル成果物ドキュメントを生成します。

| stage 引数 | 出力ファイル |
|-----------|------------|
| `requirement` | `/docs/deliverables/requirements/requirements-spec.md` |
| `basic` | `/docs/deliverables/basic-design/basic-design.md` |
| `detailed` | `/docs/deliverables/detailed-design/detailed-design.md` |
| 省略 | 上記すべて |

**生成ドキュメントの形式：**

```markdown
# {工程名}定義書

> 本書は ADR（アーキテクチャ決定記録）から自動生成されています。
> 原典: /docs/adr/ | 生成日: YYYY-MM-DD

## 目次
...

## {機能要件 / アーキテクチャ / 実装パターン}

### ADR-NNNN: {タイトル}
**概要:** {Decision}
**背景:** {Context}
**受入条件:** {Verification}
**備考:** {Consequences}

---

## 変更履歴

| ADR | 変更内容 | 置換先 | 日付 |
```

`deliverables/` は `.gitignore` や `CLAUDE.md` に記載しない（AI コンテキスト汚染を防ぐ）。

---

## インデックスファイルの管理

```markdown
# ADR Index
> Human-readable deliverables: /docs/deliverables/ (not in AI context)

## Requirement
| ID | Title | Status | Date |

## Basic Design
...

## Detailed Design
...

## Superseded / Deprecated
| ID | Title | Superseded By | Date |
```

**`docs/adr/CLAUDE.md` の作成（初回のみ提案する）：**

`docs/adr/` ディレクトリに `CLAUDE.md` を作成することで、Claude Code がそのディレクトリを訪れた際に `0000-index.md` を自動的にコンテキストへ読み込みます。`0000-index.md` は ADR コミュニティの慣習に沿った名前として維持します。

```markdown
# Architecture Decision Records
> Human-readable deliverables are in /docs/deliverables/ (not loaded into context).

@0000-index.md
```

ルートの `CLAUDE.md` への追記は不要です。`docs/adr/CLAUDE.md` が自動的にポインタとして機能します。

---

## 明示的コマンドのサポート

- **`adr:new [title]`** — 草案を提示して承認を求める（Stage もユーザーに確認する）
- **`adr:supersede [old-id] [new-id]`** — 変更提案を提示して承認を求める
- **`adr:list`** — Accepted な ADR を Stage 別に一覧表示する
- **`adr:lint [file|--all]`** — 列挙ルールで ADR を機械的に検証する（LLM 判断なし）
- **`adr:format [file|--all]`** — 自動修正可能な lint 違反を修正する（L104, L402, L405）
- **`adr:check`** — lint（errors のみ）を実行し、通過したら Verification コマンドの実行可能性を検証する
- **`adr:export [stage]`** — V字モデル成果物ドキュメントを生成する（承認不要）
