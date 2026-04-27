------
# コンテナ命名規則

<instructions>
You are an expert agent utilizing this skill.

```
{project}-{stage}-{service}
```

例: `myapp-local-db`

### Dockerラベル

| ラベル | 値 | 用途 |
|--------|-----|------|
| `com.docker.compose.project` | `{project}-{stage}` | OrbStackグループ化 |
| `com.docker.compose.service` | `{service}` | サービス識別 |
| `fleetflow.project` | プロジェクト名 | メタデータ |
| `fleetflow.stage` | ステージ名 | メタデータ |
| `fleetflow.service` | サービス名 | メタデータ |

## OrbStack連携

FleetFlowは主にmacOSのローカル開発環境での利用を想定しており、OrbStackと連携します。

- `com.docker.compose.project`ラベルでグループ化
- プロジェクト・ステージごとに整理された表示
- Docker Composeとの互換性

## DNS自動管理

`cloud up`/`cloud down`時にCloudflare DNSを自動管理：

**サブドメイン命名規則**:
```
{service}-{stage}.{domain}
```

例: `api-live.example.com`

**動作**:
- `cloud up`: サーバー作成後にAレコードを自動追加
- `cloud down`: サーバー削除前にDNSレコードを自動削除
- `dns_aliases`: 追加のCNAMEエイリアスを自動作成

**必要な環境変数**:
- `CLOUDFLARE_API_TOKEN`
- `CLOUDFLARE_ZONE_ID`
- `CLOUDFLARE_DOMAIN`

## ドキュメント構造

- **仕様書 (spec)**: Creo Memories (fleetflow atlas, category: "spec") — S1〜S10
- **設計書 (design)**: Creo Memories (fleetflow atlas, category: "design-decision") — D1〜D8
- **利用ガイド (guide)**: `docs/guide/` + Creo Memories (fleetflow atlas, category: "guide")

## 開発フェーズ

### Phase 1: MVP ✅
- KDLパーサー
- 基本CLI（up/down/ps/logs）
- Docker API統合
- OrbStack連携

### Phase 2: ビルド機能 ✅
- Dockerビルド
- 個別サービス操作
- 複数設定ファイル対応
- マルチステージビルド対応
- イメージプッシュ

### Phase 3: クラウドインフラ ✅
- クラウドプロバイダー抽象化
- さくらクラウド連携
- Cloudflare DNS連携
- CLI統合

### Phase 4: 高度な機能 ✅
- MCP（Model Context Protocol）サーバー
- Playbook機能
- CI/CDデプロイコマンド
- セルフアップデート

### Phase 5: 拡張機能 ✅
- include ディレクティブ（KDLファイル分割・glob対応）
- 変数展開（`{{ VAR }}` テンプレート構文）
- Fleet Registry（複数fleet統合管理・SSHリモートデプロイ）
- CI環境でのセルフアップデートスキップ

### Phase 6: ヘルスチェック強化 ✅
- `fleet ps` に HEALTH 列追加（healthy/unhealthy/starting 表示）
- readiness チェック（サービス起動時の準備完了確認）

### Phase 7: Platform 進化 ✅
- Control Plane（常駐デーモン + Core API + Auth0 認証）
- マルチプロジェクト横断管理
- WebUI Dashboard（Auth0 SPA SDK 統合）
- MCP Server v2（CP 経由 17 ツール）
- 詳細: Creo Memories (fleetflow atlas, category: "spec") S10
</instructions>
