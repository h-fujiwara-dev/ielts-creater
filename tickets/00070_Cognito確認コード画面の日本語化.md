# #00070 Cognito確認コード画面（Managed Login）の日本語化

- 対象リポジトリ: infra / frontend
- ステータス: 未対応
- type: feat
- 関連ドキュメント: [#00034 Cognito認証の本実装](./00034_Cognito認証の本実装.md) / [#00052 ログイン登録画面のCognitoHostedUIブランディング調整](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md) / [#00058 ゲスト機能の本番環境への反映](./00058_ゲスト機能の本番環境への反映.md)

## 実現すること

新規登録（メールアドレス/パスワード入力）後に表示される確認コード入力画面（`https://auth.band-eight.com/confirm`）が英語表示になっている。現行構成はCognito Hosted UI（Classic）で、CSS/ロゴのみカスタマイズ可能な方式のため文言（テキスト）自体はローカライズ不可。日本語化するにはAWSの新しいブランディング方式「Managed Login」へ移行する必要がある（Managed Loginは`lang`クエリパラメータでの多言語対応に対応しており、日本語（`ja`）を含む12言語をサポート）。

対応方針:

1. （infra）`aws_cognito_managed_login_branding`リソースの利用にTerraform AWSプロバイダ**v6.12.0以降**が必要なため、infraリポジトリ全体（cognito以外のモジュール: ECS/Network/S3/API Gateway/ECR/email-sender含む）のAWSプロバイダバージョン制約を`~> 5.0`から`~> 6.0`系へ引き上げる。v5→v6のメジャーアップグレードによる破壊的変更（CHANGELOG確認）がCognito以外のリソースに影響しないか事前に確認し、dev環境で`terraform plan`が意図しない差分（リソース再作成等）を出さないことを検証してからprodに適用する
2. （infra）Cognitoユーザープールの`user_pool_tier`を`ESSENTIALS`（Managed Loginのローカライズ機能に必要な最小プラン）に設定する
3. （infra）`aws_cognito_user_pool_domain`の`managed_login_version`を`2`（Managed Login）に変更する
4. （infra）`aws_cognito_managed_login_branding`リソースを追加し、webアプリクライアントにスタイルを割り当てる（既存の`aws_cognito_user_pool_ui_customization`（Hosted UI Classic用CSS、#00052/#00067で調整済み）はManaged Loginでは使われないため、置き換えまたは配色設定をManaged Login側のsettingsに移植する）
5. （infra）dev環境に適用し、実際にサインアップ〜確認コード入力までの画面がManaged Loginで表示されることを確認してからprod環境に適用する
6. （frontend）`src/auth.ts`のCognito Provider（サインイン用・`cognito-signup`用）の`authorization.params`に`lang: "ja"`を追加し、Hosted UIへの遷移時点から日本語ロケールのCookieが設定されるようにする

## 受け入れ条件

- [ ] infraリポジトリのAWSプロバイダが`~> 6.0`系に更新され、dev/prod両方で`terraform plan`/`terraform apply`が正常に通る
- [ ] Cognito以外のモジュール（ECS/Network/S3/API Gateway/ECR/email-sender）にプロバイダアップグレードによる意図しないリソース再作成・破壊的変更がないことを確認する
- [ ] Cognitoユーザープールの`user_pool_tier`が`ESSENTIALS`以上になっている
- [ ] dev/prodとも`aws_cognito_user_pool_domain`が`managed_login_version = 2`（Managed Login）になっている
- [ ] `aws_cognito_managed_login_branding`がTerraformで管理されている
- [ ] frontendのCognito認可リクエストに`lang=ja`が付与されている
- [ ] dev環境で実際に新規登録を行い、確認コード入力画面を含む一連の認証フロー（サインイン・新規登録・確認コード入力・ログアウト）が日本語で表示され、正常に動作することを手動確認する
- [ ] prod環境でも同様に確認する
- [ ] Managed Login移行前に適用していたHosted UI（Classic）の配色カスタマイズ（#00052/#00067）が、Managed Login側でも同等に反映されている、または代替方針が決まっている

## 作業ログ

- 2026-08-25 起票。band-eight.com本番環境の新規登録フローで確認コード入力画面が英語表示になっている不具合報告を受けて調査。Cognito Hosted UI（Classic）は文言のローカライズ不可であり、AWS公式のManaged Login機能への移行（Terraform AWSプロバイダv6.12.0以降が必要）が必要と判明したため起票
