# #00056 本番カスタムドメイン（band-eight.com）の導入とVercel/Cognitoへの適用

- 対象リポジトリ: infra / frontend
- ステータス: 未対応
- type: feat
- 関連ドキュメント: [#00050 本番環境のAWSインフラ構築とCICDパイプライン整備](./00050_本番環境のAWSインフラ構築とCICDパイプライン整備.md) / [#00051 frontendとbackendの本番環境への初回デプロイ](./00051_frontendとbackendの本番環境への初回デプロイ.md) / [#00052 ログイン/登録画面のCognito Hosted UIブランディング調整](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md) / [#00053 登録認証メールの迷惑フォルダ対策](./00053_登録認証メールの迷惑フォルダ対策.md)

## 実現すること

Cloudflareで新規取得した独自ドメイン`band-eight.com`を、本番運用基盤として次の3用途に適用する。

1. Vercel本番デプロイのカスタムドメイン（frontendの本番URLをband-eight.com配下にする）
2. Cognito User Poolのメール送信元ドメイン（[#00053](./00053_登録認証メールの迷惑フォルダ対策.md)のSES独自ドメイン送信化で検証対象とするドメイン）
3. Cognito Hosted UIのカスタムドメイン（[#00052](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md)のブランディング調整と合わせ、認証画面のURLをband-eight.com配下にする）

DNSはCloudflareのレジストラ機能のみを使い、ネームサーバーをVercelへ委任する方針とする（Vercel Domains上でband-eight.com配下の全DNSレコードを一元管理し、Route53等AWS側のDNSは使用しない）。frontend用・Hosted UI用（例: auth.band-eight.com）・メール送信元用のサブドメイン構成は実装着手時に確定する。

- 前提として[#00050](./00050_本番環境のAWSインフラ構築とCICDパイプライン整備.md)・[#00051](./00051_frontendとbackendの本番環境への初回デプロイ.md)が完了していること
- 本チケットは[#00052](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md)・[#00053](./00053_登録認証メールの迷惑フォルダ対策.md)がドメインを必要とする前提条件を満たすための基盤整備と位置づける

## 受け入れ条件

- [ ] band-eight.comのネームサーバーをVercelのネームサーバーに変更し、Vercelダッシュボード上でドメインの検証が完了している
- [ ] Vercel本番プロジェクトにband-eight.com（またはそのサブドメイン）をカスタムドメインとして割り当て、本番frontendに独自ドメインでアクセスできることを確認する
- [ ] SES送信ドメイン検証（DKIM/SPF）に必要なDNSレコードをVercel DNS上に追加し、band-eight.comの検証がpassしている（[#00053](./00053_登録認証メールの迷惑フォルダ対策.md)着手の前提を満たす）
- [ ] Cognito Hosted UIのカスタムドメイン用のACM証明書検証レコードをVercel DNS上に追加し、Cognito側でカスタムドメインが有効化されている（[#00052](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md)着手の前提を満たす）
- [ ] 決定したサブドメイン構成を作業ログに記録する

## 作業ログ

- 2026-08-14 起票
