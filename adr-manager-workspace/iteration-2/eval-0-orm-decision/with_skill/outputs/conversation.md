# Conversation Summary — ORM Decision (Prisma vs TypeORM)

## Exchange

**User:**
新しい Node.js バックエンドプロジェクトを始めます。ORM は Prisma と TypeORM で迷っているんですが、どちらがいいですか？型安全性を重視したいです。

**Assistant:**
型安全性を重視するなら Prisma を推奨。理由：

- Prisma は `schema.prisma` からクライアントコードを自動生成するため、DB スキーマと TypeScript 型が常に同期される（コンパイル時検出）
- TypeORM はデコレータベースで型安全性が部分的にしか保証されず、ランタイムエラーのリスクが残る
- 比較表を提示（型安全性・スキーマ管理・マイグレーション・エコシステム）
- TypeORM が有利なケース（ActiveRecord、NestJS 既存プロジェクトとの一貫性）も併記

**User:**
なるほど、Prisma で行きます。

**Assistant:**
（決定を検知 → ADR を自動作成）

📝 ADR-0001 を記録しました（Basic Design: Prisma を ORM として採用）

## ADR Auto-Creation Details

- **Trigger:** ユーザーが「Prisma で行きます」と合意 → 技術採用合意のシグナルを検知
- **Stage 推論:** フレームワーク・DB・全体アーキテクチャに関する決定 → **Basic Design**
- **Status:** Accepted（会話で合意済み）
- **Generated file:** `0001-use-prisma-as-orm.md`
- **Index updated:** `0000-index.md` に ADR-0001 を Basic Design セクションへ追加

## Notification shown to user

```
📝 ADR-0001 を記録しました（Basic Design: Prisma を ORM として採用）
```
