# Chronista Club Plugins - 開発・運用情報

> **プラグインのバージョンの SSoT は各 repo の `.claude-plugin/plugin.json`。**
> このドキュメントも `marketplace.json` の各エントリも、プラグイン版数を持たない
> （ミラーは必ずドリフトするため）。
>
> `marketplace.json` の `metadata.version` は別物で、**登録簿そのもののリビジョン**。
> プラグインの構成が変わったときだけ bump する（例: #8 ccwire 削除で 1.0.0 → 1.1.0、
> fleetflow 削除で 1.1.0 → 1.2.0）。版数同期では上げない（#5〜#7 は据え置き）。

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

### 配布ブランチについて

`marketplace.json` の `source` は `{ "source": "github", "repo": "..." }` 形式で、
**`ref` 指定が効かない**。このため **各 repo のデフォルトブランチがそのまま配布元になる**。

根拠 — 公式 marketplace (`anthropics/claude-plugins-official`) 全 291 件の `source` 形式内訳:

| 形式 | 件数 | うち `ref` / `branch` / `tag` 指定あり |
|------|-----:|-----:|
| `url` | 153 | **0** |
| `git-subdir` | 85 | **84** |
| 文字列（短縮形） | 53 | **0** |

`ref` が機能する実例は `git-subdir` 形式に限られる。そして
**`source: "github"` 形式は公式 marketplace に 1 件も存在しない**（chronista-plugins 独自）。
サポートされる保証がないため、ブランチ指定に依存した運用を組まないこと。
開発 trunk を `nightly` 等に置く場合も、デフォルトブランチは `main` のままにすること
（`nightly` をデフォルトにすると未リリース版が配布される）。

---

## プラグイン一覧

| プラグイン | リポジトリ | 提供コンポーネント |
|-----------|-----------|------------------|
| creo-memories | [claude-plugin-creo-memories](https://github.com/chronista-club/claude-plugin-creo-memories) | MCP (`creo-memories`) / Commands / Skills / Hooks |
| vantage-point | [claude-plugin-vantage-point](https://github.com/chronista-club/claude-plugin-vantage-point) | Commands / Skills / Hooks |
| chronista-style | [claude-plugin-chronista-style](https://github.com/chronista-club/claude-plugin-chronista-style) | MCP (`gitnexus`, `vantage-point`) / Commands / Skills / Hooks |
| team-bucciarati | [claude-plugin-team-bucciarati](https://github.com/chronista-club/claude-plugin-team-bucciarati) | MCP (`teamb-metrics`) / Agents / Commands / Skills |

> **各プラグインが提供する個々のコマンド / スキル / MCP ツールの一覧は、各リポジトリの
> README と `SKILL.md` が SSoT。ここには複製しない。**
>
> 以前このファイルが持っていた複製表は全面的に陳腐化していた。版数が古いだけでなく、
> creo-memories の MCP ツール名は `remember_context` / `recall_relevant` /
> `search_memories` / `forget_memory` と書かれていたが、実在するのは
> `remember` / `search` / `forget` で、**名前が全て誤りだった**。

### ⚠️ vantage-point の MCP サーバは chronista-style 側で宣言されている

`claude-plugin-vantage-point` は `.mcp.json` を持たない。VP の MCP サーバ (`vp mcp`) は
`claude-plugin-chronista-style/.mcp.json` で `gitnexus` と並べて宣言されている。

**vantage-point 単体をインストールしても MCP ツールは付いてこない**（commands / skills /
hooks のみ）。意図的な配置かどうか未確認。

### 廃止済み

- **fleetflow** — *プラグインのみ*メンテナンス終了につき marketplace から削除。
  **CLI 本体 https://github.com/chronista-club/fleetflow は継続中**（archived ではない）。
  廃止したのは「CLI の使い方ガイド」を配っていたプラグインの方だけ。
  なおこのガイドをどのプラグインも引き継いでいない（chronista-style に fleetflow
  スキルは存在しない）。Gemini CLI 向けには `gemini-skills/fleetflow/` が残っている。
- **ccwire** — marketplace から削除（#8）、repo も archived
- **ccnav** — marketplace から削除、機能は ccws (Rust CLI) に吸収

---

## 各プラグインの基本情報

複製を避けるため、ここには**リポジトリ側に書けない / 書いていない外部接続先**のみ置く。

### creo-memories

| 項目 | 値 |
|------|-----|
| **バックエンド** | https://github.com/chronista-club/creo-memories |
| **MCP URL** | https://mcp.creo-memories.in/ |
| **認証** | Auth0 |

### vantage-point

| 項目 | 値 |
|------|-----|
| **バイナリ** | `vp` コマンド（Rust製） |
| **MCP 起動** | `vp mcp` (stdio) — 宣言は chronista-style 側 |

---

## このリポジトリの役割

`claude-plugins` は**コードを持たない登録簿**で、`.claude-plugin/marketplace.json` が
上流 4 リポジトリを指す間接参照になっている。

このため**上流が進むと登録簿は黙って古くなる**。実際、`version` フィールドを持っていた頃は
リリースのたびに `chore(marketplace): sync ...` を手で積む必要があり、#2〜#9 のほぼ全てが
その同期作業だった。version を削除してこの作業自体を無くした。

**同じ理由で、このドキュメントに上流の内容を複製しないこと。**

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

---

## コントリビューション

各プラグインのリポジトリで Issue / PR を受け付けています。

---

*このファイルは版数・更新日を持たない。履歴は `git log` を参照。*
