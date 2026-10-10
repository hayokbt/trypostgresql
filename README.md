# trypostgresql

## 起動前の準備

- Docker Engine がインストールされ、起動していること
- Docker Compose v2 (`docker compose` コマンド) が使えること
- 使用するホスト側ポート `5173`、`8080`、`8081`、`5433` が空いていること
- 初回起動時はイメージや依存関係を取得するため、インターネットに接続できること

Java や Node.js をホストにインストールする必要はありません。frontend と javaback は Docker イメージのビルド時にそれぞれ必要な環境を使います。

## 全コンテナの起動

リポジトリのルート（この README.md があるディレクトリ）で実行します。

```bash
docker compose up -d --build
```

初回や Dockerfile を変更した後は `--build` を付けます。2回目以降、イメージの再ビルドが不要なら次のコマンドで起動できます。

```bash
docker compose up -d
```

## コンテナごとの起動

次のコマンドもリポジトリのルートで実行します。javaback と pgAdmin は PostgreSQL の起動完了を待つ設定です。それぞれを起動すると、依存する PostgreSQL も自動で起動します。

```bash
# PostgreSQL
docker compose up -d postgres

# pgAdmin（PostgreSQL も起動）
docker compose up -d pgadmin

# javaback（PostgreSQL も起動。初回や再ビルド時は --build を付ける）
docker compose up -d --build javaback

# frontend（初回や再ビルド時は --build を付ける）
docker compose up -d --build frontend
```

## アクセス先と接続情報

- Frontend: http://localhost:5173
- Backend: http://localhost:8081
- pgAdmin: http://localhost:8080
- PostgreSQL (ホストから接続): `localhost:5433`

pgAdmin の初期ログイン情報は `admin@example.com` / `admin` です。PostgreSQL の初期データベース、ユーザー、パスワードは `mydatabase` / `postgres` / `mypassword` です。コンテナから PostgreSQL に接続する場合は、ホスト名に `postgres`、ポートに `5432` を指定します。

## 状態確認と停止

```bash
# 起動状態を確認
docker compose ps

# 全コンテナのログを確認（Ctrl+C でログ表示を終了）
docker compose logs -f

# 特定のコンテナのログを確認
docker compose logs -f javaback

# 全コンテナを停止
docker compose down
```

個別に停止する場合は `docker compose stop postgres` のようにサービス名を指定します。`docker compose down` では DB データは削除されず、`postgres_data` ボリュームに保持されます。DB データも削除する場合は `docker compose down -v` を実行してください。この操作で保存済みデータも消去されます。

現在、Backend の `/` に画面や API はないため、起動確認でアクセスすると `404` が返ります。これはコンテナやサーバーの起動失敗を意味するものではありません。
