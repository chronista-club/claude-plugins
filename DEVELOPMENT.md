# Chronista Club Plugins - 開発・運用情報

> **ステータス**: 開発中 (v1.4.0)

## マーケットプレイス

| 項目 | 値 |
|------|-----|
| **名前** | chronista-plugins |
| **リポジトリ** | https://github.com/chronista-club/claude-plugins |
| **オーナー** | Chronista Club |

### インストール

```bash
# マーケットプレイス追加
claude plugin marketplace add chronista-club/claude-plugins

# プラグイン一覧確認
claude plugin marketplace list
```

---

## プラグイン一覧

| プラグイン | バージョン | ステータス | 提供機能 |
|-----------|-----------|-----------|---------|
| creo-memories | 1.0.0 | 🟢 公開中 | MCP, Hooks, Skills |
| vantage-point | 1.0.0 | 🟡 開発中 | Commands, Hooks |
| chronista-style | 1.0.0 | 🟢 公開中 | Commands, Skills |
| ccwire | 0.1.0 | 🟡 開発中 | MCP, Skills, Commands, Hooks |

---

## 1. creo-memories

永続記憶システム - セッションを超えて知識を蓄積

### 基本情報

| 項目 | 値 |
|------|-----|
| **リポジトリ** | https://github.com/chronista-club/claude-plugin-creo-memories |
| **バックエンド** | https://github.com/chronista-club/creo-memories |
| **MCP URL** | https://mcp.creo-memories.in/ |
| **認証** | Auth0 |

### 提供機能

#### MCP ツール

| ツール | 説明 |
|--------|------|
| `remember_context` | 記憶を保存 |
| `recall_relevant` | セマンティック検索 |
| `search_memories` | 高度な検索（フィルタ付き） |
| `list_recent_memories` | 最近の記憶一覧 |
| `forget_memory` | 記憶を削除 |
| `create_todo` | Todo作成 |
| `list_todos` | Todo一覧 |
| `update_todo` | Todo更新 |
| `complete_todo` | Todo完了 |
| `delete_todo` | Todo削除 |

#### Hooks

| イベント | 動作 |
|---------|------|
| SessionStart | 関連記憶を自動検索 |
| Stop | 重要な決定があれば保存提案 |
| PostToolUse (Write/Edit) | 設計変更時に保存提案 |
| UserPromptSubmit | 確定表現で保存提案 |

#### Skills

| スキル | 説明 |
|--------|------|
| creo-memories | 記憶システムの使い方ガイド |

### 開発中の機能 (Issues)

| # | タイトル | 優先度 |
|---|---------|--------|
| [#48](https://github.com/chronista-club/creo-memories/issues/48) | Stripe Subscriptions 課金システム実装 | 🔴 next |
| [#49](https://github.com/chronista-club/creo-memories/issues/49) | ユーザーメモリ件数リミット（Free: 500件） | 🔴 next |
| [#50](https://github.com/chronista-club/creo-memories/issues/50) | セッション中のDomain切り替え | - |
| [#42](https://github.com/chronista-club/creo-memories/issues/42) | Discord通知設定（Grafanaアラート） | - |
| [#38](https://github.com/chronista-club/creo-memories/issues/38) | 石狩リージョンへのデータバックアップ | - |

---

## 2. vantage-point

リッチダッシュボード - Markdown、HTML、画像をブラウザで表示

### 基本情報

| 項目 | 値 |
|------|-----|
| **リポジトリ** | https://github.com/chronista-club/claude-plugin-vantage-point |
| **バイナリ** | `vp` コマンド（Rust製） |

### 提供機能

#### Commands

| コマンド | 説明 |
|---------|------|
| `/dashboard` | ダッシュボード表示 |

#### MCP ツール（バイナリ経由）

| ツール | 説明 |
|--------|------|
| `show` | コンテンツを表示 |
| `clear` | ペインをクリア |
| `toggle_pane` | ペイン表示切替 |
| `restart` | Stand再起動 |

### ダッシュボード構成

```
┌─────────────┬─────────────────────┬─────────────┐
│    LEFT     │        MAIN         │    RIGHT    │
│             │                     │             │
│  Todoリスト │    コンテキスト     │  Memories   │
│  (empty時は │    リサーチ結果     │             │
│  Next候補)  │    計画/SDG         │             │
└─────────────┴─────────────────────┴─────────────┘
```

### 開発予定

- [ ] creo-memories との連携強化
- [ ] Ghostty Quick Terminal 対応検討
- [ ] リアルタイム更新

---

## 3. chronista-style

開発ワークフロー＆スキル - codeflow、SDG、fleetflow

### 基本情報

| 項目 | 値 |
|------|-----|
| **リポジトリ** | https://github.com/chronista-club/claude-plugin-chronista-style |

### 提供機能

#### Commands

| コマンド | 説明 |
|---------|------|
| `/codeflow` | ヒアリングファースト開発フロー |
| `/sdg` | 仕様・設計ガイド |

#### Skills

| スキル | 説明 |
|--------|------|
| codeflow | ヒアリングファースト開発フロー |
| spec-design-guide | 仕様・設計ドキュメント管理 |
| fleetflow | KDLベースのコンテナオーケストレーション |

---

## 開発ロードマップ

### Phase 1: 基盤整備 ✅

- [x] マーケットプレイス作成
- [x] 3プラグインの基本構造
- [x] creo-memories MCP 連携
- [x] Hooks 設定

### Phase 2: 課金・制限 🚧

- [ ] Stripe Subscriptions 実装
- [ ] ユーザーメモリ件数リミット (Free: 500件)
- [ ] プラン管理 UI

### Phase 3: ダッシュボード強化

- [ ] vantage-point 完全統合
- [ ] リアルタイム記憶表示
- [ ] Todoリスト表示

### Phase 4: エコシステム拡張

- [ ] 追加プラグイン開発
- [ ] コミュニティ貢献対応
- [ ] ドキュメント充実

---

## 運用情報

### インフラ

| サービス | 用途 |
|---------|------|
| SurrealDB | データベース |
| さくらVPS | サーバー |
| Cloudflare | CDN・DNS |
| Auth0 | 認証 |

### モニタリング

- Grafana ダッシュボード
- Discord アラート（予定）

### バックアップ

- 石狩リージョンへの定期バックアップ（予定）

---

## コントリビューション

各プラグインのリポジトリで Issue / PR を受け付けています。

---

*最終更新: 2025-12-23*
