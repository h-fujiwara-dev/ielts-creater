# #00051 frontendとbackendの本番環境への初回デプロイ

- 対象リポジトリ: frontend / backend / infra
- ステータス: 未対応
- type: feat
- 関連ドキュメント: [#00043 frontendからdevバックエンドへの接続](./00043_frontendからdevバックエンドへの接続.md) / [#00050 本番環境のAWSインフラ構築とCICDパイプライン整備](./00050_本番環境のAWSインフラ構築とCICDパイプライン整備.md) / [#00052 ログイン/登録画面のCognito Hosted UIブランディング調整](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md) / [#00053 登録認証メールの迷惑フォルダ対策](./00053_登録認証メールの迷惑フォルダ対策.md)

## 実現すること

[#00050](./00050_本番環境のAWSインフラ構築とCICDパイプライン整備.md)で整備したprod環境のAWSインフラ・CI/CDパイプラインを使い、frontend（Vercel Production環境）とbackend（prod ECS Fargate）を初めて本番公開する。

あわせて、Cloudflareで取得した独自ドメイン`band-eight.com`を本番運用基盤として適用する。用途は次の3つ:

1. Vercel本番デプロイのカスタムドメイン（frontendの本番URLをband-eight.com配下にする）
2. Cognito User Poolのメール送信元ドメイン（[#00053](./00053_登録認証メールの迷惑フォルダ対策.md)のSES独自ドメイン送信化で検証対象とするドメイン）
3. Cognito Hosted UIのカスタムドメイン（認証画面のURLをband-eight.com配下にする。[#00052](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md)のUI配色・ロゴ調整とは独立した対応で、UI自体の変更は伴わない）

DNSはCloudflareのレジストラ機能のみを使い、ネームサーバーをVercelへ委任する方針とする（Vercel Domains上でband-eight.com配下の全DNSレコードを一元管理し、Route53等AWS側のDNSは使用しない）。frontend用・Hosted UI用（例: auth.band-eight.com）・メール送信元用のサブドメイン構成は本チケット着手時に確定する。

デプロイ後、独自ドメインでの本番URLアクセスと、実際のユーザー導線（サインアップ〜主要機能、Cognito認証含む）が本番環境で問題なく動作することを確認する。

- 前提として[#00050](./00050_本番環境のAWSインフラ構築とCICDパイプライン整備.md)が完了していること
- [#00043](./00043_frontendからdevバックエンドへの接続.md)（dev版の接続対応）で行った内容の本番版に相当する
- 本チケットで確定するドメイン基盤は、[#00053](./00053_登録認証メールの迷惑フォルダ対策.md)がSES送信ドメインとして必要とする前提条件を満たす。Cognito Hosted UIのカスタムドメイン化は[#00052](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md)とは独立した対応であり、#00052の着手条件ではない

## 受け入れ条件

- [ ] band-eight.comのネームサーバーをVercelのネームサーバーに変更し、Vercelダッシュボード上でドメインの検証が完了している
- [ ] frontendをVercelのProduction環境にband-eight.com（またはそのサブドメイン）のカスタムドメインでデプロイし、本番URLでアクセスできることを確認する
- [ ] backendをprod環境のECS Fargateにデプロイし、CI/CDパイプライン経由でのデプロイが成功することを確認する
- [ ] SES送信ドメイン検証（DKIM/SPF）に必要なDNSレコードをVercel DNS上に追加し、band-eight.comの検証がpassしている（[#00053](./00053_登録認証メールの迷惑フォルダ対策.md)着手の前提を満たす）
- [ ] Cognito Hosted UIのカスタムドメイン用のACM証明書検証レコードをVercel DNS上に追加し、Cognito側でカスタムドメインが有効化されている（UIの配色・ロゴ変更は伴わない。[#00052](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md)とは独立した対応）
- [ ] 本番frontendから本番backendへの疎通・認証（Cognito）・主要APIが正常に動作することを確認する
- [ ] 決定したサブドメイン構成・本番環境の動作確認結果を作業ログに記載する

## 作業ログ

- 2026-08-13 チケット起票
- 2026-08-14 band-eight.comドメイン（Vercelカスタムドメイン・Cognitoメール送信元・Hosted UIドメイン）の適用をスコープに追加
- 2026-08-14 #00052との依存関係を解消（Hosted UIカスタムドメイン化は#00052のUI変更とは独立した対応と明記）し、SES送信ドメイン検証の受け入れ条件を本チケットに一本化
