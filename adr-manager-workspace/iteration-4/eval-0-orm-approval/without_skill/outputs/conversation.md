# ORM Selection Conversation (Without Skill)

---

## Turn 1

**User:** 新しい Node.js バックエンドプロジェクトを始めます。ORM は Prisma と TypeORM で迷っているんですが、どちらがいいですか？型安全性を重視したいです。

**Assistant:**

型安全性を重視するなら、**Prisma** をおすすめします。理由を比較してまとめます。

### Prisma の強み

- **型安全性が非常に高い**: スキーマファイル（`schema.prisma`）からTypeScriptの型が自動生成されるため、クエリの結果型がコンパイル時に保証されます
- **オートコンプリートが優秀**: Prisma Clientは生成された型を使うので、IDEのサポートが非常に強力
- **マイグレーション管理が簡単**: `prisma migrate dev` で宣言的にスキーマ変更を管理できます
- **直感的なクエリAPI**: ネストしたリレーションも型安全に扱えます

```typescript
// Prismaの例：リレーションも型安全
const user = await prisma.user.findUnique({
  where: { id: 1 },
  include: { posts: true },
});
// user.posts の型が自動的に Post[] として推論される
```

### TypeORM の特徴

- **デコレータベース**: エンティティクラスにデコレータを付けるActiveRecord/DataMapperパターン
- **型安全性はやや弱い**: クエリビルダーの結果型が `any` になるケースがある
- **柔軟性は高い**: 複雑なSQLクエリを書きやすい
- **歴史が長い**: エコシステムは成熟しているが、型定義の品質にバラつきがある

```typescript
// TypeORMの例：QueryBuilderは型推論が弱くなりがち
const users = await userRepository
  .createQueryBuilder("user")
  .leftJoinAndSelect("user.posts", "post")
  .getMany(); // User[] だが、joinした結果の型保証は限定的
```

### 結論

**型安全性を最優先にするなら Prisma** が明確に優れています。特に：

- チームでTypeScriptを使っている場合
- スキーマ駆動で開発したい場合
- マイグレーションをシンプルに管理したい場合

TypeORMが有利なのは、既存のJavaプロジェクトからの移行や、非常に複雑なSQLが多いケースくらいです。

何かほかに考慮したい点（パフォーマンス、データベースの種類、チームの経験など）はありますか？

---

## Turn 2

**User:** なるほど、Prisma で行きます

**Assistant:**

いい選択だと思います！Prisma を使い始めるための基本的なセットアップ手順を共有しますね。

### セットアップ手順

```bash
# Prismaのインストール
npm install prisma --save-dev
npm install @prisma/client

# 初期化（schema.prisma が生成される）
npx prisma init
```

### schema.prisma の基本例

```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql" // mysql, sqlite なども選択可能
  url      = env("DATABASE_URL")
}

model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  posts     Post[]
  createdAt DateTime @default(now())
}

model Post {
  id        Int    @id @default(autoincrement())
  title     String
  author    User   @relation(fields: [authorId], references: [id])
  authorId  Int
}
```

### よく使うコマンド

```bash
# マイグレーション作成・適用（開発時）
npx prisma migrate dev --name init

# Prisma Client の再生成
npx prisma generate

# データベースの中身をブラウザで確認
npx prisma studio
```

### プロジェクト構成のおすすめ

```
src/
  lib/
    prisma.ts     # Prismaクライアントのシングルトン
  models/
  routes/
prisma/
  schema.prisma
  migrations/
```

```typescript
// src/lib/prisma.ts
import { PrismaClient } from "@prisma/client";

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined;
};

export const prisma =
  globalForPrisma.prisma ??
  new PrismaClient({
    log: ["query"],
  });

if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = prisma;
```

プロジェクトの規模感や使うデータベース（PostgreSQL / MySQL / SQLiteなど）が決まっていれば、もう少し具体的なアドバイスもできますよ！
