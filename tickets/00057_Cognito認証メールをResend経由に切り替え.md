# #00056 Cognito認証メールをResend経由に切り替え（Custom Email Sender Lambda）

- 対象リポジトリ: infra
- ステータス: 未対応
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

- [ ] Resendでband-eight.com（またはそのサブドメイン）の送信ドメインが検証済みになっている
- [ ] Resend APIキーがSecrets Managerで安全に管理されている
- [ ] Custom Email Sender Lambda（KMS復号・Resend API送信）がTerraformでコード化されている
- [ ] Cognito User Pool（dev/prod）にCustom Email Sender Lambdaトリガーが設定されている
- [ ] `terraform plan` / `terraform apply`がdev/prod両方で通る
- [ ] dev環境で実際に新規登録を行い、Resend経由の確認メールが迷惑フォルダに入らず正常に届くことを手動確認する
- [ ] prod環境でも同様に確認する
- [ ] 既存の認証フロー（ログイン・新規登録・確認コード入力・ログアウト）に影響がないことを確認する

## 作業ログ

- 2026-08-17 起票。#00053のSES独自ドメイン送信方式がAWS SES production access未承認のため採用断念となったことを受け、Resend + Cognito Custom Email Sender Lambda方式で代替する
