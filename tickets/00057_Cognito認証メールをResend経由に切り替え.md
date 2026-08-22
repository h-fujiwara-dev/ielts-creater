# #00057 Cognito認証メールをResend経由に切り替え（Custom Email Sender Lambda）

- 対象リポジトリ: infra
- ステータス: 完了
- type: feat
- 関連ドキュメント: [#00053 登録認証メールの迷惑フォルダ対策](./00053_登録認証メールの迷惑フォルダ対策.md)（クローズ・本チケットで代替） / [#00034 Cognito認証の本実装](./00034_Cognito認証の本実装.md)

## 実現すること

[#00053](./00053_登録認証メールの迷惑フォルダ対策.md)でCognitoの確認メールをAWS SES独自ドメイン送信に切り替える対応を行ったが、AWS SESのproduction access申請が承認されなかったため採用を断念した。代替として、CognitoのLambdaトリガー「Custom Email Sender」を使い、Resend経由でメールを送信する方式に変更する。

Custom Email Senderトリガーは、Cognitoが確認コードを自前送信せず、KMSで暗号化したコードをLambdaに渡す仕組み。Lambda側でKMSにより復号し、任意のメールプロバイダ（本チケットではResend API）を使って送信できる。

対応方針:

1. Resend側でband-eight.com（またはそのサブドメイン）を送信ドメインとして検証する（DNSレコードをCloudflareに追加）
2. Resend APIキーを発行し、AWS Secrets Managerで管理する（Terraformにハードコードしない）
3. Cognito Custom Email Sender用のKMSキーをTerraformで作成する
4. Custom Email Sender Lambda（KMS復号 → Resend API呼び出し）をTerraformでコード化・デプロイする
5. Cognito User Pool（dev/prod）に`lambda_config.custom_email_sender`とKMSキーを設定する
6. dev/prod両環境に適用する

## 受け入れ条件

- [x] Resendでband-eight.com（またはそのサブドメイン）の送信ドメインが検証済みになっている
- [x] Resend APIキーがSecrets Managerで安全に管理されている
- [x] Custom Email Sender Lambda（KMS復号・Resend API送信）がTerraformでコード化されている
- [x] Cognito User Pool（dev/prod）にCustom Email Sender Lambdaトリガーが設定されている
- [x] `terraform plan` / `terraform apply`がdev/prod両方で通る
- [x] dev環境で実際に新規登録を行い、Resend経由の確認メールが迷惑フォルダに入らず正常に届くことを手動確認する
- [x] prod環境でも同様に確認する
- [x] 既存の認証フロー（ログイン・新規登録・確認コード入力・ログアウト）に影響がないことを確認する

## 作業ログ

- 2026-08-17 起票（当初は#00056として起票）。#00053のSES独自ドメイン送信方式がAWS SES production access未承認のため採用断念となったことを受け、Resend + Cognito Custom Email Sender Lambda方式で代替する
- 2026-08-18 別セッションが同時期に起票した「ゲスト機能（共有デモアカウント方式）の実装」チケットと番号が#00056で重複したため、本チケットを#00057に採番し直した
- 2026-08-21 Resendでband-eight.comのサブドメイン`mail.band-eight.com`を送信ドメインとして登録し、CloudflareにDKIM/MX/SPFレコードを追加して検証完了。APIキーを発行しSecrets Manager経由で管理するようTerraformコード化。KMSキー・Custom Email Sender Lambda・Cognito User Poolのlambda_config設定をdev環境に適用（infra [PR #27](https://github.com/h-fujiwara-dev/ielts-creater-infra/pull/27)）、続けてprod環境にも適用（infra [PR #31](https://github.com/h-fujiwara-dev/ielts-creater-infra/pull/31)）
- 2026-08-22 dev/prodとも実際に新規登録して手動確認したところ確認メールが届かない不具合を発見。原因は2点: (1) KMSキーのキーポリシーに`cognito-idp.amazonaws.com`への許可がなく、Cognitoが確認コードを暗号化できずLambda自体が一度も呼ばれていなかった（CloudWatch Logsも0バイト） (2) 修正後も`InvalidCiphertextException`で復号失敗。CognitoはAWS Encryption SDK形式（エンベロープ暗号化）でコードを渡す仕様のため、生の`kms:Decrypt`では復号できなかった（AWS公式ドキュメントで確認）。KMSキーポリシーへのCognito許可追加と、`@aws-crypto/client-node`（AWS Encryption SDK）による復号への切り替えで修正し、dev/prod双方に適用（infra [PR #32](https://github.com/h-fujiwara-dev/ielts-creater-infra/pull/32) / [PR #33](https://github.com/h-fujiwara-dev/ielts-creater-infra/pull/33)）。修正後、dev/prod双方で実際に新規登録から確認コード入力までの手動確認を実施し、Resend経由の確認メールが正常に届くこと・既存の認証フローに影響がないことを確認した
