> **保守終了・アーカイブ（2026-09-07）**
>
> このリポジトリの保守は終了しました。今後の開発・更新は [chronista-plugins](https://github.com/chronista-club/chronista-plugins) で行います。
> 導入・移行方法は [新カタログ chronista-plugins](https://github.com/chronista-club/chronista-plugins#旧配布先からの移行) を参照してください。
> 旧・新カタログの marketplace 名は同じ `chronista-plugins` です。旧登録を利用中の場合は、設定を退避したうえで旧登録を解除し、新カタログから再導入してください。
> 以下は保守終了時点の記録です。

# Chronista Club Plugins

Chronista開発スタイルを支えるClaude Codeプラグイン集

## インストール

```bash
# マーケットプレイスを追加
/plugin marketplace add chronista-club/claude-plugins

# プラグインメニューからインストール
/plugin
```

または個別にインストール：

```bash
/plugin install creo-memories@chronista-plugins
/plugin install vantage-point@chronista-plugins
/plugin install chronista-style@chronista-plugins
/plugin install team-bucciarati@chronista-plugins
```

> `/plugin install` は `プラグイン名@マーケットプレイス名` 形式。
> `owner/repo` 形式は解決されない。

## プラグイン一覧

| プラグイン | 説明 |
|-----------|------|
| **creo-memories** | 永続記憶システム - セッションを超えて知識を蓄積 |
| **vantage-point** | リッチダッシュボード - Markdown、HTML、画像をブラウザで表示 |
| **chronista-style** | 開発ワークフロー＆スキル - codeflow、SDG、TDD、code-review |
| **team-bucciarati** | JoJo Part 5 スタンドエージェントチーム - 開発の前後を支える品質チーム。調査・テスト・レビュー・敵対的検証の4体（コミットラインまで） |

バージョンは各プラグイン repo の `.claude-plugin/plugin.json` が SSoT。

### 廃止済み

| プラグイン | 経緯 |
|-----------|------|
| fleetflow | プラグインのメンテナンス終了につき marketplace から削除。<br>CLI 本体 [chronista-club/fleetflow](https://github.com/chronista-club/fleetflow) は継続中 |
| ccwire | marketplace から削除（#8）、repo も archived |
| ccnav | marketplace から削除、機能は ccws (Rust CLI) に吸収 |

## ライセンス

MIT
