# n8n Docker セットアップ

このプロジェクトは、Docker Composeを使用してn8n（ワークフロー自動化ツール）とPostgreSQLデータベースをセットアップするための構成です。

## 📋 概要

n8nは、様々なサービスやAPIを連携させて自動化ワークフローを作成できるオープンソースのワークフロー自動化ツールです。このセットアップでは、PostgreSQLをデータベースとして使用し、本番環境向けの設定でn8nを実行します。

## 🏗️ アーキテクチャ

- **n8n**: 最新版のn8nコンテナ（ポート5678）
- **PostgreSQL**: PostgreSQL 16-alpine（データベース）
- **ボリューム**: データ永続化のためのDockerボリューム

## 📁 プロジェクト構造

```
n8n-docker/
├── docker-compose.yml          # Docker Compose設定ファイル
├── n8n-workflows/              # n8nワークフローファイル
│   └── mail-for-purchase-info.json
└── README.md                   # このファイル
```

## 🚀 セットアップ手順

### 1. 前提条件

- Docker と Docker Compose がインストールされていること
- 環境変数ファイル（`.env`）を作成する必要があります

### 2. 環境変数の設定

プロジェクトルートに `.env` ファイルを作成し、以下の環境変数を設定してください：

```env
# PostgreSQL設定
DB_PORT=5432
POSTGRES_USER=your_postgres_user
POSTGRES_PASSWORD=your_postgres_password
POSTGRES_DB=n8n

# n8n設定
SUBDOMAIN=your_subdomain
DOMAIN_NAME=your_domain.com
GENERIC_TIMEZONE=Asia/Tokyo
```

### 3. 起動

```bash
docker-compose up -d
```

### 4. アクセス

n8nのWebインターフェースにアクセス：
```
https://your_subdomain.your_domain.com
```

ローカル環境でテストする場合：
```
http://localhost:5678
```

## 📝 ワークフロー

### ワークフローのインポート方法

1. n8nのWebインターフェースにログイン
2. ワークフローをインポート
3. `n8n-workflows/` ディレクトリからJSONファイルを選択

※個人IDに関わる部分はリプレースしてある箇所があるため、適宜自身のIDに置き換えること。

## 🔧 設定の詳細

### PostgreSQL

- **ポート**: 環境変数 `DB_PORT` で設定（デフォルト: 5432）
- **データ永続化**: `db_storage` ボリュームに保存
- **ヘルスチェック**: 5秒間隔で実行

### n8n

- **ポート**: 5678（HTTP）
- **プロトコル**: HTTPS（本番環境）
- **データ永続化**: `n8n_storage` ボリュームに保存
- **ワークフローファイル**: `./n8n-workflows` がマウントされています

## 🛠️ よくある操作

### コンテナの停止

```bash
docker-compose down
```

### コンテナの停止（ボリュームも削除）

```bash
docker-compose down -v
```

### ログの確認

```bash
# すべてのサービスのログ
docker-compose logs

# n8nのログのみ
docker-compose logs n8n

# リアルタイムでログを表示
docker-compose logs -f n8n
```

### コンテナの再起動

```bash
docker-compose restart
```

## 🔐 セキュリティに関する注意事項

1. **環境変数**: `.env` ファイルには機密情報が含まれます。Gitにコミットしないでください
2. **パスワード**: 強力なパスワードを使用してください
3. **HTTPS**: 本番環境では必ずHTTPSを使用してください
4. **ファイアウォール**: 必要に応じてポートへのアクセスを制限してください

## 📦 ボリューム

以下のDockerボリュームが作成されます：

- `db_storage`: PostgreSQLのデータを保存
- `n8n_storage`: n8nの設定とデータを保存

ボリュームの確認：
```bash
docker volume ls
```

ボリュームの詳細確認：
```bash
docker volume inspect n8n-docker_db_storage
docker volume inspect n8n-docker_n8n_storage
```

## 🐛 トラブルシューティング

### n8nが起動しない

1. PostgreSQLが正常に起動しているか確認：
   ```bash
   docker-compose ps
   ```

2. ログを確認：
   ```bash
   docker-compose logs n8n
   ```

3. 環境変数が正しく設定されているか確認

### データベース接続エラー

1. PostgreSQLのヘルスチェックが成功しているか確認
2. 環境変数の `DB_POSTGRESDB_*` 設定を確認
3. データベースコンテナのログを確認：
   ```bash
   docker-compose logs db
   ```

## 📚 参考リンク

- [n8n公式ドキュメント](https://docs.n8n.io/)
- [Docker Compose公式ドキュメント](https://docs.docker.com/compose/)
- [PostgreSQL公式ドキュメント](https://www.postgresql.org/docs/)

## 📄 ライセンス

このプロジェクトは、n8nのライセンスに従います。
