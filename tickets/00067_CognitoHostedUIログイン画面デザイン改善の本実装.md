# #00067 Cognito Hosted UIログイン画面デザイン改善の本実装

- 対象リポジトリ: infra
- ステータス: 未対応
- type: feat
- 関連ドキュメント: [#00065 Cognito Hosted UIログイン画面のデザイン改善案作成](./00065_CognitoHostedUIログイン画面のデザイン改善案作成.md) / [#00052 ログイン/登録画面のCognito Hosted UIブランディング調整](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md) / [#00034 Cognito認証の本実装](./00034_Cognito認証の本実装.md)

## 実現すること

[#00065](./00065_CognitoHostedUIログイン画面のデザイン改善案作成.md)で作成したデザイン改善案（`docs/design-drafts/`）を、実際のCognito Hosted UI（`ielts-creater-infra`）に反映する。デザイン方針の検討・モックアップ作成は#00065で完了済みのため、本チケットは実装のみを対象とする。

1. `docs/design-drafts/assets/cognito-hosted-ui.proposal.css`の内容を`ielts-creater-infra/terraform/modules/cognito/hosted-ui.css`に反映する。`.inputField-customizable`・`.errorMessage-customizable`・`.redirect-customizable`・`.logo-customizable`の新規追加、`.background-customizable`への`overflow: hidden`追加（紺色バナーと白いフォーム部分の角を統一するため）、送信ボタン・リンクの配色修正（WCAG AA基準準拠）を含む
2. `docs/design-drafts/assets/logo-mark.svg`をマスターに、Playwright（`ielts-creater-frontend`にE2E用として導入済み、#00047。新規依存追加は不要）でスクリーンショットを撮り透過PNG化してロゴ画像を作成し、`aws_cognito_user_pool_ui_customization`リソースの`image_file`属性（`filebase64(...)`）に設定する
3. `terraform plan`（dev環境）で意図した差分のみになっていることを確認する（`apply`の実行はユーザーが手動で行う。運用ルールにより本チケットの作業でのapply実行はしない）
4. dev環境で`terraform apply`後、実際のログイン・新規登録・確認コード入力・ログアウトフローに影響がないことを手動確認する

## 受け入れ条件

- [ ] `terraform/modules/cognito/hosted-ui.css`が`docs/design-drafts/assets/cognito-hosted-ui.proposal.css`（#00065）の内容で更新されている
- [ ] ロゴ画像（透過PNG、2倍解像度、目安100KB未満）が作成され、`aws_cognito_user_pool_ui_customization`の`image_file`属性に設定されている
- [ ] `terraform plan`（dev環境）で意図した差分のみ確認できる
- [ ] dev環境で`terraform apply`後、Hosted UI画面（`/login`・`/signup`）にロゴが表示され、配色・カードの角丸・入力欄・リンク・エラー表示が改善案（#00065のAfterモックアップ）通りになっていることを実機確認する
- [ ] 既存の認証フロー（ログイン・新規登録・確認コード入力・ログアウト）に影響がないことを確認する
- [ ] prod環境向けにも同様の変更を反映し、`terraform plan`（prod環境）で意図した差分のみ確認する（applyはユーザー手動実行）

## 作業ログ

- 2026-08-25 起票
