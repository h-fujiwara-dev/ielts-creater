# #00052 ログイン/登録画面のCognito Hosted UIブランディング調整

- 対象リポジトリ: infra / frontend
- ステータス: 完了
- type: fix
- 関連ドキュメント: [#00034 Cognito認証の本実装](./00034_Cognito認証の本実装.md)

## 実現すること

[#00034](./00034_Cognito認証の本実装.md)の設計判断により、ログイン・新規登録・確認コード入力はいずれもCognito Hosted UIへリダイレクトして行う方式になっている（`ielts-creater-frontend/src/components/auth/cognito-sign-in-button.tsx`参照）。dev環境での手動確認の結果、Hosted UIはAWSのデフォルト外観のまま表示されており、アプリ本体のデザイントークン（`ielts-creater-frontend/src/app/globals.css`の`--color-brand-navy: #0f172a` / `--color-brand-orange: #f97316`等）と統一されていないことが分かった。

対応方針:

1. Cognito User PoolのManaged Login branding（新UI）を使い、ロゴ・配色をアプリのデザイントークンに合わせて設定する
2. `ielts-creater-infra/terraform/modules/cognito/`にブランディング設定用のTerraformリソースを追加する
3. Hosted UIへのリダイレクト方式自体（#00034の設計判断）は変更しない

## 受け入れ条件

- [x] Cognito Hosted UI画面の配色・ロゴがアプリ本体のデザインと統一されている
- [x] `terraform plan` / `terraform apply`が通る
- [x] 既存の認証フロー（ログイン・新規登録・確認コード入力・ログアウト）に影響がないことを手動確認する

## 作業ログ

- 2026-08-13 起票
- 2026-08-13 infra [PR #10](https://github.com/h-fujiwara-dev/ielts-creater-infra/pull/10)でManaged Login brandingのTerraformリソース（`terraform/modules/cognito/hosted-ui.css`等）を追加しdevelopへマージ済み。ステータス更新が漏れていたため今回反映
