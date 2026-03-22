# Eval: Implicit Supersede Detection — with_skill

## User Message
「新しいエンドポイントの入力バリデーションを実装したいんですが、Zod を使ってスキーマを定義してみようと思います。型安全性が高くて、バリデーションロジックを共有できるので良さそうです。」

## Assistant Response

Zod の採用を検討されているのですね。型安全性の高さやフロント・バック間でのスキーマ共有は確かに大きなメリットです。

ただ、既存の決定と矛盾が生じる可能性があります。

現在 ADR-0001 で「NestJS バックエンドの DTO バリデーションには `class-validator` + `class-transformer` を使用する」と決定されています。Zod を採用した場合、この方針が置き換わります。

---

📋 **ADR 変更提案（Supersede）**

以下の変更を適用してよろしいですか？

**変更① ADR-0001 のステータス変更**
- Title: class-validator による入力バリデーション
- Status: Accepted → Superseded by ADR-0002

**変更② 新規 ADR-0002 を作成**
- Stage: Detailed Design
- Title: Zod による入力バリデーション
- Decision: `class-validator` + `class-transformer` に替えて `zod` を使用し、すべての DTO スキーマを Zod スキーマで定義する。
- Rationale: TypeScript の型推論との完全な統合・フロントエンドとのスキーマ共有が可能
- Verification: `npm run test:unit`
- Supersedes: ADR-0001

承認する場合は「OK」、修正がある場合は内容を教えてください。

---

なお、NestJS で Zod を使う場合は `nestjs-zod` などのアダプターか、`ZodValidationPipe` を自前で実装する必要があります。その点も ADR に記載しておくか、後続の設計判断として別 ADR を立てるかご確認いただけますか？
