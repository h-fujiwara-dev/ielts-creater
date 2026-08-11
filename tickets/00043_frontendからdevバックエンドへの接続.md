# #00043 frontendからdevバックエンド（AWS）への接続

- 対象リポジトリ: frontend
- ステータス: 完了
- type: chore
- 関連ドキュメント: [#00037 インフラ構成の見直し（Vercel_Supabase化）とREADME構成図の追加](./00037_インフラ構成の見直し（Vercel_Supabase化）とREADME構成図の追加.md) / [#00044 backendのAWSインフラ構築とSupabase接続](./00044_backendのAWSインフラ構築とSupabase接続.md) / [システム要件定義書 8章 アーキテクチャ](../docs/システム要件定義書.md#8-アーキテクチャ)

## 実現すること

Vercelの制約（dev環境用の一時的なデプロイが運用に見合わない）により、dev環境ではフロントエンドをVercelにデプロイしない。フロントエンドはローカル実行のまま、#00044で構築するAWS上のdevバックエンド（API Gateway経由のAPI・Cognito User Pool）に接続して、Cognito認証を含む一連の動作を確認できるようにする。

- `BACKEND_API_ORIGIN`は#00044で構築したAPI Gatewayのdevエンドポイントに依存するため、**#00044完了後に着手する**（#00044は完了済み。**[方針転換]** 当初想定していたALBではなく、コスト最適化のためAPI Gateway（HTTP API）+ VPC Link + Cloud Mapに変更されている点に注意）
  - devエンドポイント: `https://g8r1slo0d0.execute-api.ap-northeast-1.amazonaws.com`（`/actuator/health`で疎通確認済み）
- Vercelへの実デプロイは本チケットのスコープ外（将来、本番相当の別チケットで対応）

## 受け入れ条件

- [x] ローカル`.env.local`の`BACKEND_API_ORIGIN`を#00044で構築したAPI Gatewayのdevエンドポイント（`https://g8r1slo0d0.execute-api.ap-northeast-1.amazonaws.com`）に向ける
- [x] `COGNITO_CLIENT_ID`/`COGNITO_CLIENT_SECRET`/`COGNITO_ISSUER`/`COGNITO_HOSTED_UI_DOMAIN`をdevのCognito User Pool（実値）に切り替える
- [x] CognitoのApp Client許可コールバックURLに、ローカル開発用のリダイレクトURL（`http://localhost:3000/...`）が登録されていることを確認する（infra側`modules/cognito`、必要なら#00044側で対応）
- [x] ローカルでログイン〜問題生成〜採点まで、実AWS dev環境（Cognito・ECS api・Supabase）に接続した状態で一連の動作を確認する
- [x] README.mdに「ローカルfrontend + devバックエンド（AWS）」構成での接続手順を追記する
- [x] markdownlintが通る

## 作業ログ

- 2026-08-11 チケット起票
- 2026-08-12 完了: infra `terraform/envs/dev`で`terraform output`を実行しdevのCognito実値（Client ID/Secret/Issuer/Hosted UIドメイン）を取得。`callback_urls`/`logout_urls`（`http://localhost:3000/...`）は`terraform.tfvars.example`のデフォルト値のまま#00044のapply時に反映済みで、`aws cognito-idp describe-user-pool-client`で登録済みであることを確認した（追加のterraform applyは不要）。frontendのローカル`.env.local`（gitignore対象）に`BACKEND_API_ORIGIN`（devのAPI Gatewayエンドポイント）と上記Cognito値を設定し、Playwrightでログイン（Cognito Hosted UIサインアップ）〜問題生成（Reading）〜回答〜採点まで実AWS dev環境（Cognito・ECS api・Supabase）に接続した状態で一連の動作を確認した（8/8正解、ダッシュボードの総受験回数にも反映）。README.mdに「ローカルfrontend + devバックエンド（AWS）」接続手順を追記し、Cognito必須である旨（#00034で廃止済みのPhase1 no-auth前提の記述）もあわせて実態に更新。[frontend PR #35](https://github.com/h-fujiwara-dev/ielts-creater-frontend/pull/35)を作成しマージ済み
  - **副次的な発見（本チケットのスコープ外）**: Cognito Hosted UIの標準サインアップ（email+password）では`name`属性が設定されないため、backend `GET /api/v1/me`の`displayName`検証（`UserProvisioningService`/`CognitoUserAttributesClient`、#00034）が失敗し、初めてサインアップする実ユーザーは全員ログインが完了しない不具合を確認した。動作確認時はAWS管理者権限で検証用ユーザーに`name`属性を手動設定して回避したが、根本原因の修正は別チケットでの対応が必要
