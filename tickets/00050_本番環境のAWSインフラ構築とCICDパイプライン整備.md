# #00050 本番環境のAWSインフラ構築とCICDパイプライン整備

- 対象リポジトリ: infra / backend
- ステータス: 未対応
- type: feat
- 関連ドキュメント: [#00044 backendのAWSインフラ構築とSupabase接続](./00044_backendのAWSインフラ構築とSupabase接続.md) / [#00045 infraのブランチ戦略を環境ごとに分離](./00045_infraのブランチ戦略を環境ごとに分離.md) / [#00049 4リポジトリのdevelopからmainへのリリースマージ](./00049_4リポジトリのdevelopからmainへのリリースマージ.md) / [システム要件定義書 8章 アーキテクチャ](../docs/システム要件定義書.md#8-アーキテクチャ)

## 実現すること

[#00044](./00044_backendのAWSインフラ構築とSupabase接続.md)でdev環境に構築済みのAWSインフラ（VPC/API Gateway+VPC Link+Cloud Map/NAT Instance/ECS Fargate Spot/S3/ECR/Secrets Manager）と同等の構成を`terraform/envs/prod`に構築する。あわせて、backendの本番デプロイを継続的に行えるよう、GitHub ActionsによるCI/CDパイプライン（イメージビルド→ECRプッシュ→ECSサービス更新）を整備する。

- 前提として[#00049](./00049_4リポジトリのdevelopからmainへのリリースマージ.md)でinfra/backendの`main`が最新化されていること
- 作業ブランチは`prd`から作成する（[#00045](./00045_infraのブランチ戦略を環境ごとに分離.md)のブランチ戦略に従う。`main ← prd ← 作業ブランチ`）
- `terraform apply`は実AWSリソースの作成・課金が発生するため、apply直前に必ずユーザーへ確認する（#00044と同様の運用）
- prod用のシークレット・環境変数（Secrets Manager等）はdev環境と分離する

## 受け入れ条件

- [ ] `terraform/envs/prod`にdev相当のモジュール（network/api-gateway/ecs/s3/ecr等）呼び出しを定義する
- [ ] `prd`ブランチ経由で`terraform apply`を実行し、prod環境のAWSインフラを構築する（apply直前にユーザーへ確認する）
- [ ] backendリポジトリにprod向けCI/CDワークフロー（イメージビルド・ECRプッシュ・ECSサービス更新）を追加する
- [ ] 構築したprod環境のAPIエンドポイントに疎通確認を行う
- [ ] prod用シークレット・環境変数がdev環境と分離されていることを確認する
- [ ] 各リポジトリでmarkdownlint・既存テストスイートが通る

## 作業ログ

- 2026-08-13 チケット起票
