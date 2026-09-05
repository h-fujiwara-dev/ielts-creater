# #00076 Cognito MFAの有効化

- 対象リポジトリ: infra
- ステータス: 未対応
- type: fix
- 関連ドキュメント: [docs/システム要件定義書.md](../docs/システム要件定義書.md)（7.3セキュリティ要件） / [#00067 CognitoHostedUIログイン画面デザイン改善の本実装](./00067_CognitoHostedUIログイン画面デザイン改善の本実装.md)

## 実現すること

2026-09-05に実施したインフラのプライベート設定監査（`ielts-creater-infra`）で判明した事項。

`terraform/modules/cognito/main.tf`の`aws_cognito_user_pool`は`mfa_configuration = "OFF"`（dev/prod共通）となっており、多要素認証が無効な状態。パスワード漏洩時の被害軽減のため、TOTPベースのMFAを有効化する。

Hosted UI（`auth.band-eight.com`、#00067でブランディング済み）にMFA設定・チャレンジ画面が新たに表示されるようになるため、既存のブランディングとの見た目の整合性も確認する。

## 受け入れ条件

- [ ] MFAを`ON`（必須）にするか`OPTIONAL`（任意）にするか方針を決定し、本項目に理由を記録する
- [ ] `aws_cognito_user_pool`の`mfa_configuration`/`software_token_mfa_configuration`をTerraformで設定する
- [ ] dev環境でサインアップ〜MFA設定（TOTPアプリ登録）〜ログインフローが問題なく動作することを確認する
- [ ] Hosted UIのMFA関連画面（QRコード表示・コード入力画面等）に表示崩れがないか確認する
- [ ] prod環境へ`terraform apply`し反映する

## 作業ログ

- 2026-09-05 起票
