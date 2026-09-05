# #00075 API Gatewayへの認証レイヤー追加（多層防御）

- 対象リポジトリ: infra
- ステータス: 未対応
- type: fix
- 関連ドキュメント: [docs/システム要件定義書.md](../docs/システム要件定義書.md)（7.3セキュリティ要件・8.3 AWSインフラ構成図）

## 実現すること

2026-09-05に実施したインフラのプライベート設定監査（`ielts-creater-infra`の`origin/develop`/`origin/prd`両ブランチ）で判明した事項。

`terraform/modules/api-gateway/main.tf`の`aws_apigatewayv2_route`（`ANY /{proxy+}`・`ANY /`）には`authorizer_id`/`authorization_type`が一切設定されておらず、API Gateway層での認証チェックが存在しない。認証はSpring Boot（Spring Security Resource Server）側でのJWT検証に完全委譲されている設計であり、アーキテクチャ図の意図には沿っているが、アプリ側の実装ミス・設定漏れが発生した場合にAPI Gateway層での歯止めがない状態になっている。

Cognito User Poolをissuerとする`aws_apigatewayv2_authorizer`（`jwt_configuration`）を追加し、認証が必要なルートに適用することで多層防御（defense-in-depth）を実現する。

## 受け入れ条件

- [ ] backend側で外部公開すべき未認証エンドポイント（ヘルスチェック等、API Gateway経由でアクセスされるものに限る）の有無を洗い出す
- [ ] Cognito User Poolをissuerとする`aws_apigatewayv2_authorizer`（JWT）を`api-gateway`モジュールに追加する
- [ ] 認証が必要なルートに`authorizer_id`を設定し、未認証アクセスがdev環境で401/403で拒否されることを確認する
- [ ] 既存の正規リクエスト（Cognitoアクセストークン付き）が引き続き成功することをdev環境で確認する
- [ ] dev/prod両環境へ`terraform apply`し反映する

## 作業ログ

- 2026-09-05 起票
