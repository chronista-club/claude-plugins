------
# 併用パターン

<instructions>
You are an expert agent utilizing this skill.

```typescript
// 公式SDKで接続
import { Surreal } from 'surrealdb';
// sdbkで型定義
import { userTable, User } from './db';
import { validateEntity } from '@sdbk/core';

const db = new Surreal();
await db.connect('ws://localhost:8000/rpc');

// sdbkの型とバリデーションを活用
const userData = {
  name: 'John',
  email: 'john@example.com',
};

const validated = validateEntity(userTable, userData);
if (validated.success) {
  // 公式SDKで操作
  const [user] = await db.create<User>('user', validated.data);
}
```

## 開発

### セットアップ

```bash
git clone https://github.com/veskel01/sdbk.git
cd sdbk

# 依存関係インストール
bun install

# ビルド
bun run build

# テスト
bun run test

# リント
bun run lint

# フォーマット
bun run format
```

### プロジェクト構造

```
sdbk/
├── packages/
│   └── core/          # コア型定義
├── scripts/           # ビルドスクリプト
└── turbo.json         # Turbo設定
```

## リソース

- **GitHub**: https://github.com/Veskel01/sdbk
- **Issues**: https://github.com/Veskel01/sdbk/issues
- **SurrealDB公式**: https://surrealdb.com/docs

## ユースケース

### いつsdbkを使うべきか

- TypeScriptプロジェクトで型安全なスキーマ定義が必要
- コンパイル時の型検証を活用したい
- スキーマ駆動開発を行いたい
- バリデーションロジックを一元化したい

### 公式SDKのみで十分な場合

- シンプルなCRUD操作のみ
- 動的なスキーマを扱う
- ランタイムのみの型チェックで十分
</instructions>
