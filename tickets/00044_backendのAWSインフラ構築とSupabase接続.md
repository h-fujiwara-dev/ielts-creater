# #00044 backendのAWSインフラ構築とSupabase接続

- 対象リポジトリ: infra / backend
- ステータス: 完了
- type: feat
- 関連ドキュメント: [#00037 インフラ構成の見直し（Vercel_Supabase化）とREADME構成図の追加](./00037_インフラ構成の見直し（Vercel_Supabase化）とREADME構成図の追加.md) / [#00043 frontendからdevバックエンド（AWS）への接続](./00043_frontendからdevバックエンドへの接続.md) / [システム要件定義書 8章 アーキテクチャ](../docs/システム要件定義書.md#8-アーキテクチャ)

## 実現すること

dev環境にAWSインフラ（VPC/API Gateway/NAT Instance/ECS Fargate(api)/S3/ECR、Secrets Manager）を構築し、backendをDocker化してECS Fargateで稼働させる。DB接続はRDSではなくSupabase（マネージドPostgreSQL）に切り替える（#00037で確定済みの方針）。

- infra側の新規モジュールは、既存の`terraform/modules/cognito/`と同じファイル構成規約（`main.tf`/`variables.tf`/`outputs.tf`/`versions.tf`）に従う
- Supabaseのアカウント登録・プロジェクト作成自体はユーザーの手作業（接続文字列を取得した後の実装・Terraform反映をClaudeが担う）
- **[実装時の方針転換]** ALBは使わず、コスト最適化のためAPI Gateway（HTTP API）+ VPC Link + Cloud Map（ECS Service Discovery）でECSタスクへ直接ルーティングする構成に変更した（ALB固定費 約$18/月 → VPC Link 約$7.2/月+従量課金）。同様にNAT GatewayはNAT Instance（EC2、自前でiptables MASQUERADE構成）に、ECS ServiceはFargate Spotに変更し、dev環境の月額コストを概算$85〜90 → 概算$16〜17+従量課金まで削減した
- `terraform apply`は実AWSリソースの作成・課金が発生するため、apply直前に必ずユーザーへ確認する
- 完了後、確定したAPI Gatewayのdevエンドポイントを#00043に共有する（#00043はこのチケット完了後に着手する）

## 受け入れ条件

- [x] （ユーザー作業）Supabaseプロジェクトを作成し、接続文字列（`sslmode=require`）を取得する
- [x] backendに本番向け`Dockerfile`を新規作成する
- [x] infra: `modules/network`（VPC、Public/Private Subnet x2AZ、IGW、NAT Instance、Cloud Map namespace）を新規作成する
- [x] infra: `modules/api-gateway`（HTTP API、VPC Link、Cloud Map private integration。当初予定の`modules/alb`から方針転換）を新規作成する
- [x] infra: `modules/ecs`（ECSクラスタ、api用Fargate Spotサービス・タスク定義、Cloud Map Service Discovery登録）を新規作成する
- [x] infra: `modules/s3`（Listening音声用バケット、非公開設定）を新規作成する
- [x] infra: `modules/ecr`（backendイメージ用リポジトリ）を新規作成する
- [x] `envs/dev/main.tf`から上記モジュールを呼び出す
- [x] Secrets ManagerにSupabase接続文字列・OpenAI APIキー等のシークレットを格納し、ECSタスク定義から参照する
- [x] backendに`application-prod.yml`を新規作成し、Supabase接続（SSL必須）を反映する
- [x] ECRへbackendイメージをpushする
- [x] `terraform apply`でdev環境を構築する（実AWS課金が発生するため、apply直前に必ずユーザーへ確認する）
- [x] API Gateway経由でbackend APIの疎通を確認する（当初予定のALBから方針転換）
- [x] 確定したAPI Gatewayエンドポイントを#00043に共有する
- [x] 各リポジトリでmarkdownlint・既存テストスイートが通る

## 作業ログ

- 2026-08-11 チケット起票
- 2026-08-12 infra/backend実装・dev環境構築・疎通確認を完了
  - コスト最適化のためALB→API Gateway（HTTP API + VPC Link + Cloud Map）、NAT Gateway→NAT Instance（t4g.micro）、ECS Fargate→Fargate Spot（0.25vCPU/0.5GB）に方針転換
  - Supabaseの直接接続ホスト（`db.<project>.supabase.co`）はIPv6のみでECS（IPv4のみのVPC）から到達できないことが判明したため、Session Pooler接続文字列（`aws-0-<region>.pooler.supabase.com`、IPv4対応）に切り替えて対応した
  - AWSアカウントがFree Tier対象インスタンスタイプのみ許可されていたため、NAT InstanceはEC2 `t4g.nano`ではなく`t4g.micro`を使用した
  - 疎通確認: `https://g8r1slo0d0.execute-api.ap-northeast-1.amazonaws.com/actuator/health` → `{"status":"UP"}`（200）を確認。このエンドポイントを#00043の`BACKEND_API_ORIGIN`に使用する
  - infra側PR: <https://github.com/h-fujiwara-dev/ielts-creater-infra/pull/7>
  - backend側PR: <https://github.com/h-fujiwara-dev/ielts-creater-backend/pull/17>
