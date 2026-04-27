------
# 実行

<instructions>
You are an expert agent utilizing this skill.

```bash
chmod +x schema/apply.sh
./schema/apply.sh
```

## 削除戦略

### removed/配下で管理

削除が必要な定義は`removed/`に移動し、手動で実行します。

```sql
-- schema/removed/2024-01-15_remove_old_field.surql
REMOVE FIELD old_field ON TABLE user;
REMOVE INDEX old_index ON TABLE user;
REMOVE TABLE deprecated_table;
```

### 削除スクリプト

```bash
#!/bin/bash
# schema/remove.sh

echo "WARNING: This will remove schema definitions!"
read -p "Continue? (y/N): " -n 1 -r
echo

if [[ $REPLY =~ ^[Yy]$ ]]; then
  for file in schema/removed/*.surql; do
    echo "Removing: $(basename $file)"
    surreal sql --endpoint "$ENDPOINT" \
      --user "$USER" --pass "$PASS" \
      --ns "$NS" --db "$DB" < "$file"
  done
fi
```

## CI/CD統合

### GitHub Actions

```yaml
name: Apply Schema

on:
  push:
    branches: [main]
    paths:
      - 'schema/**'

jobs:
  apply-schema:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Install SurrealDB
        run: |
          curl -sSf https://install.surrealdb.com | sh
      
      - name: Apply Schema
        env:
          SURREAL_ENDPOINT: ${{ secrets.SURREAL_ENDPOINT }}
          SURREAL_USER: ${{ secrets.SURREAL_USER }}
          SURREAL_PASS: ${{ secrets.SURREAL_PASS }}
          SURREAL_NS: production
          SURREAL_DB: app
        run: |
          chmod +x schema/apply.sh
          ./schema/apply.sh
```

## ベストプラクティス

### 1. 常にOVERWRITEを使用

```sql
-- ✅ 冪等
DEFINE TABLE user SCHEMAFULL OVERWRITE;
DEFINE FIELD name ON TABLE user TYPE string OVERWRITE;

-- ❌ 2回目の実行でエラー
DEFINE TABLE user SCHEMAFULL;
DEFINE FIELD name ON TABLE user TYPE string;
```

### 2. ファイルは論理単位で分割

```
01_tables/
├── user.surql          # ユーザー関連
├── post.surql          # 投稿関連
└── comment.surql       # コメント関連
```

### 3. カテゴリ順序を守る

```
01_tables/      # 最初（依存なし）
02_indexes/     # テーブル後
03_relations/   # リレーションは最後
04_permissions/ # 全体設定は最後
```

### 4. 環境別スキーマ

```
schema/
├── base/           # 全環境共通
├── development/    # 開発専用
└── production/     # 本番専用
```

```bash
# 開発環境
./schema/apply.sh schema/base
./schema/apply.sh schema/development

# 本番環境
./schema/apply.sh schema/base
./schema/apply.sh schema/production
```

## トラブルシューティング

### 問題1: フィールド型変更

```sql
-- ❌ 型変更はエラーになる場合がある
DEFINE FIELD age ON TABLE user TYPE int OVERWRITE;
-- 既存データがstring型の場合エラー

-- ✅ データ移行を先に実行
UPDATE user SET age = <int>age WHERE age != NONE;
DEFINE FIELD age ON TABLE user TYPE int OVERWRITE;
```

### 問題2: インデックス再構築

```sql
-- OVERWRITE時に自動再構築される
DEFINE INDEX idx_email ON TABLE user FIELDS email UNIQUE OVERWRITE;
```

### 問題3: 依存関係エラー

```sql
-- ❌ postテーブルがまだ存在しない
DEFINE FIELD post ON TABLE comment TYPE record<post> OVERWRITE;

-- ✅ カテゴリ順序で解決
-- 01_tables/post.surql で post を先に定義
-- 01_tables/comment.surql で comment を定義
```

## 公式リソース

- DEFINE文: https://surrealdb.com/docs/surrealql/statements/define
- スキーマ: https://surrealdb.com/docs/surrealql/datamodel
</instructions>
