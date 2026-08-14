# #00050 本番環境のAWSインフラ構築とCICDパイプライン整備

- 対象リポジトリ: infra / backend
- ステータス: 完了
- type: feat
- 関連ドキュメント: [#00044 backendのAWSインフラ構築とSupabase接続](./00044_backendのAWSインフラ構築とSupabase接続.md) / [#00045 infraのブランチ戦略を環境ごとに分離](./00045_infraのブランチ戦略を環境ごとに分離.md) / [#00049 4リポジトリのdevelopからmainへのリリースマージ](./00049_4リポジトリのdevelopからmainへのリリースマージ.md) / [システム要件定義書 8章 アーキテクチャ](../docs/システム要件定義書.md#8-アーキテクチャ)

## 実現すること

[#00044](./00044_backendのAWSインフラ構築とSupabase接続.md)でdev環境に構築済みのAWSインフラ（VPC/API Gateway+VPC Link+Cloud Map/NAT Instance/ECS Fargate Spot/S3/ECR/Secrets Manager）と同等の構成を`terraform/envs/prod`に構築する。あわせて、backendの本番デプロイを継続的に行えるよう、GitHub ActionsによるCI/CDパイプライン（イメージビルド→ECRプッシュ→ECSサービス更新）を整備する。

- 前提として[#00049](./00049_4リポジトリのdevelopからmainへのリリースマージ.md)でinfra/backendの`main`が最新化されていること
- 作業ブランチは`prd`から作成する（[#00045](./00045_infraのブランチ戦略を環境ごとに分離.md)のブランチ戦略に従う。`main ← prd ← 作業ブランチ`）
- `terraform apply`は実AWSリソースの作成・課金が発生するため、apply直前に必ずユーザーへ確認する（#00044と同様の運用）
- prod用のシークレット・環境変数（Secrets Manager等）はdev環境と分離する

## 受け入れ条件

- [x] `terraform/envs/prod`にdev相当のモジュール（network/api-gateway/ecs/s3/ecr等）呼び出しを定義する
- [x] `prd`ブランチ経由で`terraform apply`を実行し、prod環境のAWSインフラを構築する（apply直前にユーザーへ確認する）
- [x] backendリポジトリにprod向けCI/CDワークフロー（イメージビルド・ECRプッシュ・ECSサービス更新）を追加する
- [x] 構築したprod環境のAPIエンドポイントに疎通確認を行う
- [x] prod用シークレット・環境変数がdev環境と分離されていることを確認する
- [x] 各リポジトリでmarkdownlint・既存テストスイートが通る

## 作業ログ

- 2026-08-13 チケット起票
- 2026-08-14 `prd`ブランチが#00045新設以降mainの更新に追随できておらず古いままだったため、まず`prd`をmainに同期するPRを作成・マージ（infra PR #14）
- 2026-08-14 `terraform/envs/prod`一式を実装。あわせてbackend CI/CDがGitHub Secretsへ長期AWS資格情報を持たずに済むよう、GitHub Actions OIDC federation用のIAM Role（`ielts-creater-prod-github-actions-deploy`）を追加。OIDC IDプロバイダはAWSアカウントに1つしか作成できないため`terraform/bootstrap`に追加した。infra PR #15、backend PR #24（deploy-prod.yml追加）を作成・マージ
- 2026-08-14 prod専用のSupabaseプロジェクトを新規作成（データをdevと完全分離）。OpenAI APIキーはdevと共用
- 2026-08-14 `terraform apply`を2段階（network/ecr/s3 → backendイメージpush → 残り全体）で実行し、56リソースを構築（実AWS課金発生、事前にユーザー確認済み）。dev同様、実行直前にNAT Instanceは引き続きt4g.micro・ECSはFargate Spotを踏襲
  - 構築中に2件の不具合を発見・修正:
    1. Cognito `domain_prefix`のデフォルト値に環境名を含めていたため、`modules/cognito`が付与する`-${environment}`と重複し`hosted_ui_domain`が`ielts-creater-prod-prod...`になる不具合。`terraform plan`の出力確認で発覚し、apply前に修正（infra PR #16）
    2. Apple Silicon（arm64）でビルドしたDockerイメージをFargate（linux/amd64）にpushしたため`CannotPullContainerError`でタスク起動に失敗。`docker build --platform linux/amd64`で再ビルド・再pushし解消
  - 疎通確認: `https://bmngtsrht2.execute-api.ap-northeast-1.amazonaws.com/actuator/health` → `{"status":"UP"}`（200）を確認
  - backendリポジトリのrepository variables（`AWS_DEPLOY_ROLE_ARN`/`AWS_REGION`/`ECR_REPOSITORY`/`ECS_CLUSTER`/`ECS_SERVICE`/`ECS_TASK_FAMILY`）を設定し、以後`main`へのpushで`deploy-prod.yml`が自動デプロイする状態にした
