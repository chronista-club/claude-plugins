------
# 環境変数

<instructions>
You are an expert agent utilizing this skill.

```kdl
environment {
    DATABASE_URL "postgres://localhost:5432/mydb"
    DEBUG "true"
    NODE_ENV "development"
}
```

- キーと値をペアで指定
- 複数行で定義可能
- **マージ時は両方の値が結合される**（後の定義が優先）

### ボリュームマウント

```kdl
volumes {
    volume "./data" "/var/lib/postgresql/data"
    volume "/config" "/etc/config" read_only=true
}
```

**構文**: `volume <host_path> <container_path> [options]`

| パラメータ | 必須 | 説明 |
|-----------|------|------|
| 第1引数 | Yes | ホスト側のパス（相対パスは自動で絶対パスに変換） |
| 第2引数 | Yes | コンテナ内のパス |
| `read_only` | - | 読み取り専用（デフォルト: false） |

### コマンド実行

```kdl
service "db" {
    image "postgres:16"
    command "postgres -c max_connections=200"
}
```

- コンテナ起動時のコマンドを上書き
- スペースで自動的に引数分割

### 依存関係

```kdl
service "web" {
    image "node:20-alpine"
    depends_on "db" "redis"
}
```

- 起動順序の制御に使用
- スペース区切りで複数指定可能

### 依存サービス待機（Exponential Backoff）

```kdl
service "api" {
    image "myapp/api:latest"
    depends_on "db" "redis"
    wait_for {
        max_retries 23        // 最大リトライ回数
        initial_delay 1000    // 初回待機時間（ミリ秒）
        max_delay 30000       // 最大待機時間（ミリ秒）
        multiplier 2.0        // 待機時間の増加倍率
    }
}
```

K8sのReadiness Probeコンセプトを取り入れた、依存サービスの準備完了待機機能です。

| パラメータ | デフォルト | 説明 |
|-----------|-----------|------|
| `max_retries` | 23 | 最大リトライ回数 |
| `initial_delay` | 1000 | 初回待機時間（ミリ秒） |
| `max_delay` | 30000 | 最大待機時間（ミリ秒） |
| `multiplier` | 2.0 | 待機時間の増加倍率 |

**待機時間の計算**（Exponential Backoff）:
```
delay = initial_delay * multiplier^attempt
```

デフォルト設定での待機パターン: 1秒→2秒→4秒→8秒→16秒→30秒（上限）...

```kdl
// デフォルト設定を使用
service "api" {
    depends_on "db"
    wait_for  // 全てデフォルト値
}
```

### 再起動ポリシー

```kdl
service "db" {
    image "postgres:16"
    restart "unless-stopped"
}
```

**対応する値**:

| 値 | 説明 |
|----|------|
| `no` | 再起動しない（デフォルト） |
| `always` | 常に再起動 |
| `on-failure` | 異常終了時のみ再起動 |
| `unless-stopped` | 明示的に停止されない限り再起動（推奨） |

**用途**: ホスト再起動後にコンテナを自動復旧させる場合に使用

### Dockerビルド設定

```kdl
service "api" {
    image "myapp/api:latest"  // ビルド後のイメージタグ

    // 明示的なビルド設定
    dockerfile "services/api/Dockerfile"
    context "."
    target "production"

    build_args {
        RUST_VERSION "1.75"
        NODE_VERSION "20"
    }
}
```

| パラメータ | 説明 |
|-----------|------|
| `dockerfile` | Dockerfileのパス |
| `context` | ビルドコンテキスト（デフォルト: プロジェクトルート） |
| `target` | マルチステージビルドのターゲット |
| `build_args` | ビルド引数 |

**規約ベース検出**: `services/{name}/Dockerfile` が自動検出されます。

**検索順序**:
1. `./services/{service-name}/Dockerfile`
2. `./{service-name}/Dockerfile`
3. `./Dockerfile.{service-name}`

### ヘルスチェック設定

```kdl
service "db" {
    image "postgres:16"
    healthcheck {
        test "pg_isready -U postgres"
        interval 30
        timeout 3
        retries 3
        start_period 10
    }
}
```

| パラメータ | デフォルト | 説明 |
|-----------|-----------|------|
| `test` | - | ヘルスチェックコマンド（必須） |
| `interval` | 30 | チェック間隔（秒） |
| `timeout` | 3 | タイムアウト（秒） |
| `retries` | 3 | リトライ回数 |
| `start_period` | 10 | 起動待機時間（秒） |

## クラウドインフラ定義

### プロバイダー設定

```kdl
providers {
    sakura-cloud {
        zone "tk1a"
        // 認証はusacloud configから自動取得
    }

    cloudflare {
        account-id env="CF_ACCOUNT_ID"
        // 認証は環境変数から
    }
}
```

### サーバー定義（ステージ内）

```kdl
stage "dev" {
    server "app-server" {
        provider "sakura-cloud"
        plan core=4 memory=4
        disk size=100 os="ubuntu-24.04"
        ssh-key "~/.ssh/id_ed25519.pub"

        // DNSエイリアス（オプション）
        dns_aliases "app" "api" "www"
    }
}
```

| パラメータ | 説明 |
|-----------|------|
| `provider` | 使用するクラウドプロバイダー |
| `plan` | サーバースペック（コア数、メモリ） |
| `disk` | ディスクサイズとOS |
| `ssh-key` | SSH公開鍵のパス |
| `dns_aliases` | DNSエイリアス（CNAMEレコード） |

## Fleet Registry 定義

`fleet-registry.kdl` で複数fleetとサーバーを統合管理：

```kdl
registry "my-org"

fleet "api" {
    path "/opt/apps/fleets/api"
    description "APIサーバー"
}

fleet "frontend" {
    path "/opt/apps/fleets/frontend"
}

server "vps-01" {
    provider "sakura-cloud"
    plan "4core-8gb"
    ssh-host "153.x.x.x"
    ssh-user "root"
    deploy-path "/opt/apps"
}

route "api:live" {
    server "vps-01"
}
```

| ノード | 説明 |
|--------|------|
| `registry` | Registry名 |
| `fleet` | FleetFlowプロジェクト定義 |
| `server` | サーバー定義（SSH接続情報含む） |
| `route` | fleet:stage → server のマッピング |

## サービスマージ機能

複数ファイルで同じサービスを定義した場合、設定がマージされます：

```kdl
// flow.kdl（ベース設定）
service "api" {
    image "myapp:latest"
    ports { port 8080 3000 }
    environment { NODE_ENV "production" }
}

// flow.local.kdl（ローカルオーバーライド）
service "api" {
    environment { DATABASE_URL "localhost:5432" }
}

// 結果:
// - image: "myapp:latest" (保持)
// - ports: [8080:3000] (保持)
// - env: { NODE_ENV: "production", DATABASE_URL: "localhost:5432" } (マージ)
```

**マージルール**:

| フィールドタイプ | ルール | 例 |
|----------------|--------|-----|
| `Option<T>` | 後の定義が`Some`なら上書き | image, command, build, healthcheck |
| `Vec<T>` | 後の定義が空でなければ上書き | ports, volumes, depends_on |
| `HashMap<K, V>` | 両方をマージ（後の定義が優先） | environment |

## 設定ファイル検索順序

FleetFlowは以下の優先順位で設定ファイルを検索します：

1. 環境変数 `FLEETFLOW_CONFIG_PATH`
2. カレントディレクトリ:
   - `flow.local.kdl` (ローカル専用)
   - `.flow.local.kdl`
   - `flow.kdl` (標準)
   - `.flow.kdl`
3. `.fleetflow/` ディレクトリ
4. `~/.config/fleetflow/flow.kdl` (グローバル)

## 完全な例

### ローカル開発環境

```kdl
project "myapp"

// ステージ定義
stage "local" {
    service "db"
    service "redis"
    service "web"
}

stage "live" {
    service "db"
    service "redis"
    service "web"
    service "cdn"
}

// PostgreSQL
service "db" {
    image "postgres:16-alpine"
    restart "unless-stopped"
    ports {
        port 5432 5432
    }
    environment {
        POSTGRES_DB "myapp"
        POSTGRES_USER "myapp"
        POSTGRES_PASSWORD "secret"
    }
    volumes {
        volume "./data/postgres" "/var/lib/postgresql/data"
    }
    healthcheck {
        test "pg_isready -U myapp"
        interval 10
        timeout 5
        retries 5
    }
}

// Redis
service "redis" {
    image "redis:7-alpine"
    ports {
        port 6379 6379
    }
    healthcheck {
        test "redis-cli ping"
    }
}

// Webアプリ
service "web" {
    image "node:20-alpine"
    ports {
        port 3000 3000
    }
    environment {
        NODE_ENV "development"
        DATABASE_URL "postgres://myapp:secret@db:5432/myapp"
        REDIS_URL "redis://redis:6379"
    }
    volumes {
        volume "./app" "/app"
    }
    command "npm run dev"
    depends_on "db" "redis"
    wait_for {
        max_retries 10
        initial_delay 1000
    }
}

// CDN（本番のみ）
service "cdn" {
    image "nginx:alpine"
    ports {
        port 80 80
        port 443 443
    }
    volumes {
        volume "./nginx.conf" "/etc/nginx/nginx.conf" read_only=true
    }
}
```

### クラウドインフラ設定

```kdl
project "myapp"

providers {
    sakura-cloud { zone "tk1a" }
    cloudflare { account-id env="CF_ACCOUNT_ID" }
}

stage "dev" {
    // さくらのクラウドでサーバー作成
    server "app-server" {
        provider "sakura-cloud"
        plan core=4 memory=4
        disk size=100 os="ubuntu-24.04"
        ssh-key "~/.ssh/id_ed25519.pub"
        dns_aliases "app" "api"
    }

    service "api"
    service "db"
}

service "api" {
    image "myapp/api:latest"
    ports { port 3000 3000 }
}

service "db" {
    image "postgres:16"
    ports { port 5432 5432 }
}
```
</instructions>
