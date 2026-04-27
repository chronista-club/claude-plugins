------
# スタンド別活用

<instructions>
You are an expert agent utilizing this skill.

- **Purple Haze**: `query` で全体像把握 → `context` でシンボル深堀り → `impact` で影響範囲特定
- **Moody Blues**: `detect_changes` で diff の影響フロー検出 → `impact` で変更シンボルの blast radius 確認
- **Spice Girl**: `context` でテスト対象の依存関係把握 → `query` でテスト対象の実行フロー理解

## serena — シンボリックコード解析

コードのシンボル構造を理解し、精密な読み書きを行う。

### 主なツール
| ツール | 用途 |
|--------|------|
| `find_symbol` | シンボルを名前パスで検索 |
| `get_symbols_overview` | ファイル内のシンボル一覧 |
| `find_referencing_symbols` | シンボルの参照元を検索 |
| `replace_symbol_body` | シンボルの定義を置換 |

### スタンド別活用
- **Purple Haze**: シンボルの依存関係を辿って設計意図を理解
- **Moody Blues**: 変更シンボルの参照元を確認、リグレッションリスク評価
- **Spice Girl**: テスト対象の公開 API とメソッドシグネチャを正確に把握

## context7 — ライブラリドキュメント

ライブラリの最新ドキュメントとコード例を取得。

### 主なツール
| ツール | 用途 |
|--------|------|
| `resolve-library-id` | ライブラリ ID の解決 |
| `query-docs` | ドキュメント・コード例の取得 |

### スタンド別活用
- **Purple Haze**: 技術調査でライブラリの API 仕様・ベストプラクティスを確認
- **Spice Girl**: テストフレームワークの API・パターンを確認

## linear — Issue 管理

Linear の Issue・プロジェクト・サイクルを操作。

### 主なツール
| ツール | 用途 |
|--------|------|
| `get_issue` | Issue の詳細取得 |
| `save_issue` | Issue の作成・更新 |
| `list_issues` | Issue 一覧 |
| `get_issue_status` | ステータス確認 |

### スタンド別活用
- **Aerosmith**: Issue Pipeline で Issue コンテキストの取得・ステータス管理
- **Sticky Fingers**: PR 作成時にステータスを "In Progress" に、マージ後に "Done" に

## Vantage Point CLI（VP TUI 連携時）

VP TUI が起動中の場合、Sex Pistols が Worker 管理に使用する。

### 主なコマンド
| コマンド | 用途 |
|---------|------|
| `vp process start --dir <path>` | 指定ディレクトリで SP デーモンを起動。TUI にタブとして追加可能に |
| `vp pane show --content <text>` | PP ペインにコンテンツを表示（markdown/html/log/url） |
| `vp pane clear` | PP ペインをクリア |
| `vp pane show --target <name>` | 特定プロジェクト（Worker）の PP に表示 |

### スタンド別活用
- **Sex Pistols**: Worker 環境で `vp process start` → TUI タブ追加。Worker が `vp pane show` で進捗表示
</instructions>
