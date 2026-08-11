# #00043 frontendからdevバックエンド（AWS）への接続

- 対象リポジトリ: frontend
- ステータス: 未対応
- type: chore
- 関連ドキュメント: [#00037 インフラ構成の見直し（Vercel_Supabase化）とREADME構成図の追加](./00037_インフラ構成の見直し（Vercel_Supabase化）とREADME構成図の追加.md) / [#00044 backendのAWSインフラ構築とSupabase接続](./00044_backendのAWSインフラ構築とSupabase接続.md) / [システム要件定義書 8章 アーキテクチャ](../docs/システム要件定義書.md#8-アーキテクチャ)

## 実現すること

Vercelの制約（dev環境用の一時的なデプロイが運用に見合わない）により、dev環境ではフロントエンドをVercelにデプロイしない。フロントエンドはローカル実行のまま、#00044で構築するAWS上のdevバックエンド（API Gateway経由のAPI・Cognito User Pool）に接続して、Cognito認証を含む一連の動作を確認できるようにする。

- `BACKEND_API_ORIGIN`は#00044で構築したAPI Gatewayのdevエンドポイントに依存するため、**#00044完了後に着手する**（#00044は完了済み。**[方針転換]** 当初想定していたALBではなく、コスト最適化のためAPI Gateway（HTTP API）+ VPC Link + Cloud Mapに変更されている点に注意）
  - devエンドポイント: `https://g8r1slo0d0.execute-api.ap-northeast-1.amazonaws.com`（`/actuator/health`で疎通確認済み）
- Vercelへの実デプロイは本チケットのスコープ外（将来、本番相当の別チケットで対応）

## 受け入れ条件

- [ ] ローカル`.env.local`の`BACKEND_API_ORIGIN`を#00044で構築したAPI Gatewayのdevエンドポイント（`https://g8r1slo0d0.execute-api.ap-northeast-1.amazonaws.com`）に向ける
- [ ] `COGNITO_CLIENT_ID`/`COGNITO_CLIENT_SECRET`/`COGNITO_ISSUER`/`COGNITO_HOSTED_UI_DOMAIN`をdevのCognito User Pool（実値）に切り替える
- [ ] CognitoのApp Client許可コールバックURLに、ローカル開発用のリダイレクトURL（`http://localhost:3000/...`）が登録されていることを確認する（infra側`modules/cognito`、必要なら#00044側で対応）
- [ ] ローカルでログイン〜問題生成〜採点まで、実AWS dev環境（Cognito・ECS api・Supabase）に接続した状態で一連の動作を確認する
- [ ] README.mdに「ローカルfrontend + devバックエンド（AWS）」構成での接続手順を追記する
- [ ] markdownlintが通る

## 作業ログ

- 2026-08-11 チケット起票
