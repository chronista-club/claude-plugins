------
# ボリューム管理

<instructions>
You are an expert agent utilizing this skill.

```kdl
// 開発用: ソースコードをマウント
volumes {
    volume "./src" "/app/src"
}

// データ永続化
volumes {
    volume "./data/postgres" "/var/lib/postgresql/data"
}

// 設定ファイル（読み取り専用）
volumes {
    volume "./config.yml" "/etc/app/config.yml" read_only=true
}
```

### 環境変数

```kdl
env {
    // データベース接続
    DATABASE_URL "postgres://user:pass@db:5432/app"

    // 開発モード
    NODE_ENV "development"
    RUST_LOG "debug"

    // サービス間通信
    API_URL "http://api:3000"
}
```

### 依存関係と待機

```kdl
// 起動順序の制御
service "web" {
    depends_on "db" "redis"

    // Exponential Backoffで依存サービスの準備を待機
    wait_for {
        max_retries 23        // 最大リトライ回数
        initial_delay 1000    // 初回待機時間（ミリ秒）
        max_delay 30000       // 最大待機時間（ミリ秒）
        multiplier 2.0        // 待機時間の増加倍率
    }
}
```

### ヘルスチェック

```kdl
// データベースのヘルスチェック
service "db" {
    image "postgres:16-alpine"
    healthcheck {
        test "pg_isready -U postgres"
        interval 10      // チェック間隔（秒）
        timeout 5        // タイムアウト（秒）
        retries 5        // リトライ回数
        start_period 10  // 起動待機時間（秒）
    }
}

// Redisのヘルスチェック
service "redis" {
    image "redis:7-alpine"
    healthcheck {
        test "redis-cli ping"
    }
}
```

### 再起動ポリシー

```kdl
service "db" {
    image "postgres:16-alpine"
    restart "unless-stopped"  // ホスト再起動後も自動復旧
}
```

| 値 | 説明 |
|----|------|
| `no` | 再起動しない（デフォルト） |
| `always` | 常に再起動 |
| `on-failure` | 異常終了時のみ再起動 |
| `unless-stopped` | 明示的に停止されない限り再起動（推奨） |
</instructions>
