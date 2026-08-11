# #00044 backendのAWSインフラ構築とSupabase接続

- 対象リポジトリ: infra / backend
- ステータス: 未対応
- type: feat
- 関連ドキュメント: [#00037 インフラ構成の見直し（Vercel_Supabase化）とREADME構成図の追加](./00037_インフラ構成の見直し（Vercel_Supabase化）とREADME構成図の追加.md) / [#00043 frontendからdevバックエンド（AWS）への接続](./00043_frontendからdevバックエンドへの接続.md) / [システム要件定義書 8章 アーキテクチャ](../docs/システム要件定義書.md#8-アーキテクチャ)

## 実現すること

dev環境にAWSインフラ（VPC/ALB/NAT Gateway/ECS Fargate(api)/S3/ECR、Secrets Manager）を構築し、backendをDocker化してECS Fargateで稼働させる。DB接続はRDSではなくSupabase（マネージドPostgreSQL）に切り替える（#00037で確定済みの方針）。

- infra側の新規モジュールは、既存の`terraform/modules/cognito/`と同じファイル構成規約（`main.tf`/`variables.tf`/`outputs.tf`/`versions.tf`）に従う
- Supabaseのアカウント登録・プロジェクト作成自体はユーザーの手作業（接続文字列を取得した後の実装・Terraform反映をClaudeが担う）
- ALBはdev環境ではHTTPで運用する（独自ドメイン+ACM証明書は将来の本番相当チケットで検討し、今回は対象外とする）
- `terraform apply`は実AWSリソースの作成・課金が発生するため、apply直前に必ずユーザーへ確認する
- 完了後、確定したALBのdevエンドポイントを#00043に共有する（#00043はこのチケット完了後に着手する）

## 受け入れ条件

- [ ] （ユーザー作業）Supabaseプロジェクトを作成し、接続文字列（`sslmode=require`）を取得する
- [ ] backendに本番向け`Dockerfile`を新規作成する
- [ ] infra: `modules/network`（VPC、Public/Private Subnet x2AZ、IGW）を新規作成する
- [ ] infra: `modules/alb`（ALB、ターゲットグループ、リスナールール）を新規作成する
- [ ] infra: `modules/ecs`（ECSクラスタ、api用Fargateサービス・タスク定義）を新規作成する
- [ ] infra: `modules/s3`（Listening音声用バケット、非公開設定）を新規作成する
- [ ] infra: `modules/ecr`（backendイメージ用リポジトリ）を新規作成する
- [ ] `envs/dev/main.tf`から上記モジュールを呼び出す
- [ ] Secrets ManagerにSupabase接続文字列・OpenAI APIキー等のシークレットを格納し、ECSタスク定義から参照する
- [ ] backendに`application-prod.yml`を新規作成し、Supabase接続（SSL必須、HikariCP設定）を反映する
- [ ] ECRへbackendイメージをpushする
- [ ] `terraform apply`でdev環境を構築する（実AWS課金が発生するため、apply直前に必ずユーザーへ確認する）
- [ ] ALB経由でbackend APIの疎通を確認する
- [ ] 確定したALBエンドポイントを#00043に共有する
- [ ] 各リポジトリでmarkdownlint・既存テストスイートが通る

## 作業ログ

- 2026-08-11 チケット起票
