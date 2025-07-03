# DevSpace学習ガイド

このガイドは、DevSpaceクイックスタートプロジェクトを使用してDevSpaceの理解を深めるための実践的な手順書です。

## 1. DevSpaceとは何か

DevSpaceは、Kubernetes上でのアプリケーション開発を簡素化するオープンソースツールです。主な特徴：

- **リアルタイム開発**: ローカル開発とKubernetes環境を直接同期
- **高速イテレーション**: コード変更を即座にクラスターに反映
- **DevOps統合**: CI/CDパイプラインとシームレスに統合
- **チーム協業**: 開発環境の標準化とデプロイの自動化

## 2. 前提条件

### 必要なツール
- Docker Desktop (Kubernetes有効化)
- kubectl
- DevSpace CLI

### インストール確認
```bash
# Docker確認
docker --version

# Kubernetes確認
kubectl version --client

# DevSpace確認 (未インストールの場合は次のセクションを参照)
devspace --version
```

## 3. DevSpaceのインストール

### macOS
```bash
# Homebrewを使用
brew install devspace

# または直接ダウンロード
curl -s -L "https://github.com/loft-sh/devspace/releases/latest" | sed -nE 's!.*"([^"]*devspace-darwin-amd64)".*!https://github.com\1!p' | xargs -n 1 curl -L -o devspace && chmod +x devspace && sudo mv devspace /usr/local/bin
```

### Linux
```bash
curl -s -L "https://github.com/loft-sh/devspace/releases/latest" | sed -nE 's!.*"([^"]*devspace-linux-amd64)".*!https://github.com\1!p' | xargs -n 1 curl -L -o devspace && chmod +x devspace && sudo mv devspace /usr/local/bin
```

### Windows
```powershell
# Chocolateyを使用
choco install devspace

# または手動でダウンロード
# https://github.com/loft-sh/devspace/releases からダウンロード
```

## 4. 実践学習手順

### ステップ1: DevSpaceプロジェクトの初期化

```bash
# 現在のディレクトリでDevSpaceを初期化
devspace init

# 対話形式で以下の質問に答える:
# - Dockerfile location: ./Dockerfile
# - Context path: ./
# - Container port: 3000
# - Kubernetes namespace: default (または任意の名前)
```

### ステップ2: 設定ファイルの確認

初期化後、`devspace.yaml`が生成されます。このファイルを確認：

```bash
# 設定ファイルの内容を確認
cat devspace.yaml
```

### ステップ3: 開発環境の起動

```bash
# DevSpaceを使用してアプリケーションを開発モードで起動
devspace dev

# このコマンドは以下を実行します:
# 1. Dockerイメージをビルド
# 2. Kubernetesクラスターにデプロイ
# 3. ファイル同期の開始
# 4. ポートフォワーディングの設定
# 5. ログストリーミングの開始
```

### ステップ4: リアルタイム開発の体験

1. **ファイル編集**: `index.js`を編集
   ```javascript
   // 例: 24行目のテキストを変更
   <li>Edit this text in <code>index.js</code> and save the file</li>
   ↓
   <li>DevSpaceでリアルタイム開発を体験中！</li>
   ```

2. **自動同期の確認**: ファイルを保存すると自動的にクラスターに同期
3. **ブラウザで確認**: http://localhost:3000 で変更を確認

### ステップ5: DevSpaceコマンドの学習

#### 基本コマンド
```bash
# 開発環境の起動
devspace dev

# 本番環境へのデプロイ
devspace deploy

# アプリケーションの削除
devspace purge

# 設定の表示
devspace print

# ログの確認
devspace logs

# シェルへのアクセス
devspace enter

# ポートフォワーディング
devspace open
```

#### 高度なコマンド
```bash
# 特定のプロファイルでの実行
devspace dev --profile production

# 設定値の上書き
devspace dev --set images.app.tag=v1.0.0

# 依存関係の管理
devspace build-dependencies
devspace deploy-dependencies

# プラグインの管理
devspace add plugin [plugin-name]
devspace remove plugin [plugin-name]
```

## 5. 設定ファイルの深掘り

### devspace.yaml の構造

```yaml
version: v2beta1
name: my-app

# Docker画像の設定
images:
  app:
    image: my-app
    dockerfile: ./Dockerfile

# デプロイの設定
deployments:
  - name: my-app
    helm:
      chart:
        name: component-chart
        repo: https://charts.devspace.sh
      values:
        containers:
          - image: my-app
            name: app

# 開発時の設定
dev:
  # ファイル同期
  sync:
    - path: ./
      container: /app
  
  # ポートフォワーディング
  ports:
    - port: "3000"
  
  # 自動再起動
  autoReload:
    paths:
      - ./
    deployments:
      - my-app
```

## 6. 実践的な学習プロジェクト

### プロジェクト1: 環境変数の管理
```yaml
# devspace.yaml に環境変数を追加
dev:
  env:
    - name: NODE_ENV
      value: development
    - name: DEBUG
      value: "true"
```

### プロジェクト2: 複数コンテナの管理
```yaml
# データベースコンテナの追加
deployments:
  - name: my-app
    # ... 既存の設定 ...
  - name: database
    helm:
      chart:
        name: component-chart
        repo: https://charts.devspace.sh
      values:
        containers:
          - image: postgres:13
            name: db
```

### プロジェクト3: プロファイルの使用
```yaml
# 異なる環境用のプロファイル
profiles:
  - name: production
    merge:
      images:
        app:
          tag: latest
      deployments:
        - name: my-app
          helm:
            values:
              replicas: 3
```

## 7. デバッグとトラブルシューティング

### よくある問題と解決方法

1. **ポートの競合**
   ```bash
   # 使用中のポートを確認
   lsof -i :3000
   
   # 異なるポートを使用
   devspace dev --port 3001
   ```

2. **ファイル同期の問題**
   ```bash
   # 同期状態の確認
   devspace sync --verbose
   
   # 手動同期
   devspace sync --upload-only
   ```

3. **イメージビルドの問題**
   ```bash
   # 強制リビルド
   devspace build --force-rebuild
   
   # キャッシュクリア
   devspace reset
   ```

### 便利なデバッグコマンド
```bash
# 詳細なログ表示
devspace dev --verbose

# 設定の妥当性確認
devspace validate

# リソースの状態確認
devspace status

# 設定値の確認
devspace print --show-values
```

## 8. 次のステップ

DevSpaceの基本を習得したら、以下を学習してください：

1. **Helm統合**: カスタムHelmチャートの作成
2. **CI/CD統合**: GitHub Actions、GitLab CI/CDとの統合
3. **クラスター管理**: 複数クラスターでの開発
4. **チーム開発**: 設定の共有と標準化
5. **プロダクション運用**: 本番環境での最適化

### 参考リンク
- [DevSpace公式ドキュメント](https://devspace.sh/docs/)
- [DevSpace GitHub](https://github.com/loft-sh/devspace)
- [DevSpace Community](https://devspace.sh/community)
- [DevSpace Examples](https://github.com/loft-sh/devspace/tree/main/examples)

## 9. 学習チェックリスト

- [ ] DevSpaceの基本概念を理解した
- [ ] devspace initでプロジェクトを初期化できる
- [ ] devspace devでリアルタイム開発ができる
- [ ] ファイル同期の仕組みを理解した
- [ ] devspace.yamlの基本構造を理解した
- [ ] 基本的なトラブルシューティングができる
- [ ] 環境変数やプロファイルを設定できる
- [ ] 複数コンテナの管理ができる
- [ ] 本番環境にデプロイできる
- [ ] チーム開発での設定共有を理解した

---

このガイドを通じて、DevSpaceの強力な機能を実際に体験し、Kubernetes開発の効率化を実感してください。