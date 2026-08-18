# #00037 インフラ構成の見直し（フロントエンドVercel化・DBをSupabase化）とREADME構成図の追加

- 対象リポジトリ: root / infra / backend / frontend
- ステータス: 完了
- type: docs
- 関連ドキュメント: [システム要件定義書 8章 アーキテクチャ](../docs/システム要件定義書.md#8-アーキテクチャ)

## 実現すること

当初は「README.mdにAWS一本構成（ECS Fargate + RDS + Cognito + S3 + Polly）のインフラ構成図を追加する」というスコープだったが、着手前にインフラ要件そのものを見直すことになった。

- **フロントエンド（Next.js）をVercelでホスティング**する（ECS Fargateから移行）
- **データベースをSupabase（マネージドPostgreSQL）に変更**する（RDSから移行）
- バックエンド（Spring Boot API）はECS Fargate継続、認証はAmazon Cognito継続、Listening音声ストレージはAmazon S3継続

調査の結果、infraリポジトリ（Terraform）はCognitoモジュールとtfstate bootstrapのみ実装済みで、VPC/ALB/NAT Gateway/ECS/RDS/S3(音声用)は未着手（Phase 3予定）と判明した。つまり既存のRDS/ECS webのTerraformコードを削除する作業は発生せず、これから書くPhase 3の設計方針をVercel/Supabase前提に変えるだけで済む。backend/frontendも本番向け設定（`application-prod.yml`・Cognito接続）は未実装のため、実質的な変更は主にドキュメント（システム要件定義書8章、README、ロードマップ、backendの実装規約メモ、frontendのプライバシーポリシー）に閉じる。

- システム要件定義書8章・README・ロードマップをVercel+Supabase構成に更新し、統合したインフラ構成図（Mermaid）をREADMEに追加する
- infra/backend/frontend各リポジトリの、Supabase/Vercel前提に書き換えが必要なドキュメント記述を追随させる

## 受け入れ条件

- [x] システム要件定義書8章（8.1採用技術一覧・8.2全体アーキテクチャ図・8.3AWSインフラ構成図・8.4非機能要件の実現方式）をVercel+Supabase構成に更新する
- [x] システム要件定義書9章に本アーキテクチャ決定を記録する
- [x] README.md（技術スタック・インフラ構成図・リポジトリ一覧）をVercel+Supabase構成に更新する
- [x] ロードマップ.md Phase3の説明をVercel/Supabase前提に更新する
- [x] infraリポジトリ: README.mdの「未構築」リスト・今後のモジュール設計方針にSupabase/Vercel前提（RDSモジュール不要・ECSはapi用のみ）を反映する
- [x] backendリポジトリ: `docs/実装規約.md`のPhase3記述をSupabase接続前提に更新し、`.env.example`にSupabase接続関連変数を追記する（`実装規約.md`は対応済み。`.env.example`へのSupabase接続変数追記は、ユーザー判断により対応不要としクローズ）
- [x] frontendリポジトリ: `docs/legal/プライバシーポリシー.md`のAmazon RDS記載をSupabaseに更新する
- [x] 各リポジトリでmarkdownlint（該当するもの）が通る

## 作業ログ

- 2026-08-11 チケット起票
- 2026-08-11 着手前にユーザーからインフラ要件変更（フロントエンドVercel化・DBをSupabase化）の依頼を受け、ヒアリング・関連リポジトリ調査の上でチケットを拡張
- 2026-08-11 root分を実装。システム要件定義書8章（採用技術一覧・全体アーキテクチャ図・AWSインフラ構成図・非機能要件の実現方式）と9章未決事項、README.md（技術スタック・インフラ構成図・リポジトリ一覧）、ロードマップ.md（Phase3説明）をVercel+Supabase構成に更新。markdownlint通過を確認
- 2026-08-11 infra/backend/frontendに追随。infra: [PR #6](https://github.com/h-fujiwara-dev/ielts-creater-infra/pull/6)でREADME.mdを更新。backend: 他ブランチ（`feat/00033-real-ai-audio-integration`）の未コミット変更を壊さないよう`git worktree`で作業し、[PR #15](https://github.com/h-fujiwara-dev/ielts-creater-backend/pull/15)で`docs/実装規約.md`を更新（`.env.example`は#00033マージ後に追記予定のため保留）。frontend: 同様に`git worktree`で作業し、[PR #32](https://github.com/h-fujiwara-dev/ielts-creater-frontend/pull/32)で`docs/legal/プライバシーポリシー.md`を更新。各PRともCI green
- 2026-08-17 backendの`.env.example`へのSupabase接続変数追記はユーザー判断により対応不要としクローズ。ステータスを完了に更新
