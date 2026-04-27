---

### 2. 議論からの知識保存（関連記憶の自動付加）

**シナリオ**: 設計議論を行い、決定事項を記録したい

```
ユーザー: 認証システムをどうするか議論しよう

Gemini: [議論を進める]

ユーザー: じゃあAuth0でOAuth2.0を使う方向で

Gemini: 承知しました。この決定をCreo Memoriesに保存しますか？

💡 **保存提案**
- カテゴリ: design
- タグ: [authentication, oauth, auth0]
- 内容: Auth0を使用したOAuth2.0認証を採用...

ユーザー: はい、保存して

Gemini: [remember を実行]

保存完了。Context Engineが関連記憶を発見：
🔗 **関連する過去の記憶**
1. 「セッション管理にJWTを採用」(類似度: 0.85)
2. 「APIキー認証の設計方針」(類似度: 0.72)
```

---
# 3. 過去の決定を参照

<instructions>
You are an expert agent utilizing this skill.

**シナリオ**: 以前の設計決定を思い出したい

```
ユーザー: 認証周りってどうなってたっけ？

Gemini: [search を実行]

過去のメモリから以下が見つかりました：

📝 **認証システム設計決定** (2025-12-10)
- Auth0を使用したOAuth2.0認証を採用
- マネージドサービスで運用負荷軽減

関連: #authentication #oauth #auth0
```

---

### 4. プロジェクト開始時のコンテキスト読み込み

**シナリオ**: 久しぶりにプロジェクトに戻った

```
ユーザー: このプロジェクトの概要を教えて

Gemini: [search でカテゴリ別に検索]

📚 **プロジェクトメモリから取得した情報**

### アーキテクチャ
- SurrealDB + Qdrant のハイブリッド構成
- Cloudflare Workers AIで埋め込み生成

### 最近の決定事項
1. Context Engine導入でプロアクティブ記憶提供
2. Atlas階層構造で知識を整理

詳細を確認したい項目はありますか？
```

---

## 高度なワークフロー

### 5. ラベルを使った整理

**シナリオ**: 重要なメモリにラベルを付けて管理

```typescript
// 「重要」ラベルを作成
mcp_creo-memories_label_create({
  name: "重要",
  color: "#FF0000"
})

// メモリにラベルを付与
mcp_creo-memories_label_attach({
  memory_id: "memory:...",
  label_id: "label:..."
})

// ラベルでフィルタして検索
mcp_creo-memories_search({
  query: "設計決定",
  labelIds: ["label:..."]
})
```

---

### 6. Atlasで知識を階層整理

**シナリオ**: プロジェクトごとにメモリを整理

```typescript
// プロジェクトAtlasを作成
mcp_creo-memories_create_atlas({
  name: "Creo Memories",
  description: "Creo Memoriesプロジェクトの知識ベース"
})

// サブAtlasを作成
mcp_creo-memories_create_atlas({
  name: "アーキテクチャ",
  parent_id: "atlas:...",
  description: "設計・アーキテクチャに関する記憶"
})

// ツリー構造を確認
mcp_creo-memories_get_atlas_tree({
  atlas_id: "atlas:..."
})
```

---

### 7. デバッグ知識の蓄積

**シナリオ**: バグを解決し、その知識を記録

```
ユーザー: このエラーの原因わかった？

Gemini: はい、原因は環境変数の設定漏れでした。

この解決策をメモリに保存しますか？

💡 **保存提案**
- カテゴリ: debug
- タグ: [embedding, cloudflare, config-error]
- 内容:
  # MCPサーバー起動エラー: Embedding設定
  ## 症状
  ConnectionError: Embedding API key が設定されていません
  ## 原因
  EMBEDDING_PROVIDER=cloudflare 設定時に必須の環境変数が不足
  ## 解決策
  以下を.envに追加:
  - CLOUDFLARE_ACCOUNT_ID
  - CLOUDFLARE_API_TOKEN
```

---

### 8. 時系列での振り返り

**シナリオ**: 特定期間の決定事項を確認

```typescript
mcp_creo-memories_search({
  category: "design",
  fromDate: "2025-12-01T00:00:00Z",
  toDate: "2025-12-31T23:59:59Z"
})

📅 **2025年12月の設計決定**

1. **12/10**: Auth0 OAuth認証採用
2. **12/14**: Living Documentation原則導入
3. **12/16**: Cloudflare Workers AI移行
```

---

### 9. チーム共有でメモリを横断検索

**シナリオ**: チームメンバーの設計メモリを全員で共有

```typescript
// チーム作成
mcp_creo-memories_team_create({
  name: "creo-dev",
  ownerId: "users:makoto",
  description: "Creo開発チーム"
})

// メンバー招待
mcp_creo-memories_team_invite({
  teamId: "teams:creo-dev",
  userId: "users:alice",
  role: "member"
})

// プロジェクトAtlasをチームに共有
mcp_creo-memories_share_atlas({
  atlasId: "atlas:creo-memories",
  teamId: "teams:creo-dev",
  permission: "read",
  inheritChildren: true
})

// → チームメンバーがsearchすると、共有Atlas配下のメモリも結果に含まれる
```

---

### 10. メモリ変更をリアクティブに購読

**シナリオ**: 特定カテゴリのメモリ変更を監視

```typescript
// 設計変更の購読を作成
mcp_creo-memories_subscribe_memories({
  name: "設計変更の監視",
  filter: { category: "design" },
  events: ["memory:created", "memory:updated"]
})

// 定期的に通知を確認（drain方式）
mcp_creo-memories_check_notifications({ limit: 20 })

// 不要になったら購読を削除
mcp_creo-memories_unsubscribe_memories({
  subscriptionId: "subscriptions:..."
})
```

---

## ベストプラクティス

### 保存タイミング

✅ 保存すべき時:
- 重要な設計決定が確定した
- バグの根本原因と解決策が判明した
- 新しい技術やパターンを学んだ
- 議論の結論が出た

❌ 保存不要な時:
- 一時的な作業メモ
- 明らかに陳腐化する情報
- 個人的な好みレベルの話

### 内容の質

✅ 良い例:
```markdown
# APIレート制限の設計

## 決定事項
- 認証済みユーザー: 1000 req/min
- 未認証: 100 req/min

## 理由
- DDoS対策
- 公平なリソース分配

## 実装
Redis + sliding window
```

❌ 悪い例:
```
レート制限を入れた
```

### タグの付け方

- **具体的**: `auth0` > `authentication` > `security`
- **一貫性**: 既存タグを再利用
- **階層的**: 大カテゴリ + 詳細タグ

```typescript
tags: ["infrastructure", "vps", "sakura-cloud"]
tags: ["design", "api", "rate-limiting"]
```
</instructions>
