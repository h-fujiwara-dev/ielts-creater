# #00051 frontendとbackendの本番環境への初回デプロイ

- 対象リポジトリ: frontend / backend / infra
- ステータス: 完了
- type: feat
- 関連ドキュメント: [#00043 frontendからdevバックエンドへの接続](./00043_frontendからdevバックエンドへの接続.md) / [#00050 本番環境のAWSインフラ構築とCICDパイプライン整備](./00050_本番環境のAWSインフラ構築とCICDパイプライン整備.md) / [#00052 ログイン/登録画面のCognito Hosted UIブランディング調整](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md) / [#00053 登録認証メールの迷惑フォルダ対策](./00053_登録認証メールの迷惑フォルダ対策.md)

## 実現すること

[#00050](./00050_本番環境のAWSインフラ構築とCICDパイプライン整備.md)で整備したprod環境のAWSインフラ・CI/CDパイプラインを使い、frontend（Vercel Production環境）とbackend（prod ECS Fargate）を初めて本番公開する。

あわせて、Cloudflareで取得した独自ドメイン`band-eight.com`を本番運用基盤として適用する。用途は次の3つ:

1. Vercel本番デプロイのカスタムドメイン（frontendの本番URLをband-eight.com配下にする）
2. Cognito User Poolのメール送信元ドメイン（[#00053](./00053_登録認証メールの迷惑フォルダ対策.md)のSES独自ドメイン送信化で検証対象とするドメイン）
3. Cognito Hosted UIのカスタムドメイン（認証画面のURLをband-eight.com配下にする。[#00052](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md)のUI配色・ロゴ調整とは独立した対応で、UI自体の変更は伴わない）

DNSは当初ネームサーバーをVercelへ委任する方針だったが、着手時にCloudflare APIトークンにRegistrar（ネームサーバー変更）権限がないことが判明。VercelがA/CNAMEレコード方式を`[recommended]`として案内していたため、Cloudflareを引き続きDNS管理者として維持し、必要なレコード（Aレコード・ACM検証CNAME・SES検証TXT/DKIM CNAME・Cognitoカスタムドメイン用CNAME）をCloudflare DNS APIで個別追加する方式に変更した。frontend用はapex（band-eight.com）、Hosted UI用は`auth.band-eight.com`のサブドメイン構成とした。

デプロイ後、独自ドメインでの本番URLアクセスと、実際のユーザー導線（サインアップ〜主要機能、Cognito認証含む）が本番環境で問題なく動作することを確認する。

- 前提として[#00050](./00050_本番環境のAWSインフラ構築とCICDパイプライン整備.md)が完了していること
- [#00043](./00043_frontendからdevバックエンドへの接続.md)（dev版の接続対応）で行った内容の本番版に相当する
- 本チケットで確定するドメイン基盤は、[#00053](./00053_登録認証メールの迷惑フォルダ対策.md)がSES送信ドメインとして必要とする前提条件を満たす。Cognito Hosted UIのカスタムドメイン化は[#00052](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md)とは独立した対応であり、#00052の着手条件ではない

## 受け入れ条件

- [x] band-eight.comをVercelプロジェクトに追加し、Aレコード（Cloudflare DNS）でVercel Edge Networkに到達できることを確認する（ネームサーバー完全委任からAレコード方式に変更、理由は上記）
- [x] frontendをVercelのProduction環境にband-eight.comのカスタムドメインでデプロイし、本番URLでアクセスできることを確認する
- [x] backendをprod環境のECS Fargateにデプロイし、CI/CDパイプライン経由でのデプロイが成功することを確認する
- [x] SES送信ドメイン検証（DKIM/SPF）に必要なDNSレコードをCloudflare DNS上に追加し、band-eight.comの検証がpassしている（[#00053](./00053_登録認証メールの迷惑フォルダ対策.md)着手の前提を満たす）
- [x] Cognito Hosted UIのカスタムドメイン用のACM証明書検証レコードをCloudflare DNS上に追加し、Cognito側でカスタムドメインが有効化されている（UIの配色・ロゴ変更は伴わない。[#00052](./00052_ログイン登録画面のCognitoHostedUIブランディング調整.md)とは独立した対応）
- [x] 本番frontendから本番backendへの疎通・認証（Cognito）・主要APIが正常に動作することを確認する
- [x] 決定したサブドメイン構成・本番環境の動作確認結果を作業ログに記載する

## 作業ログ

- 2026-08-13 チケット起票
- 2026-08-14 band-eight.comドメイン（Vercelカスタムドメイン・Cognitoメール送信元・Hosted UIドメイン）の適用をスコープに追加
- 2026-08-14 #00052との依存関係を解消（Hosted UIカスタムドメイン化は#00052のUI変更とは独立した対応と明記）し、SES送信ドメイン検証の受け入れ条件を本チケットに一本化
- 2026-08-14 frontendをVercelに初セットアップ（プロジェクト作成・GitHub連携・Production環境変数設定・初回デプロイ）。Cloudflare APIトークンにRegistrar権限がなかったためAレコード方式（`76.76.21.21`）に変更しband-eight.comを疎通させた
- 2026-08-14 infra: `auth.band-eight.com`用ACM証明書（us-east-1）・SES domain identity/DKIM・Cognitoカスタムドメイン対応を`terraform/envs/prod`に実装。ACM検証・SES検証（DKIM/SPFとも`Success`）用のDNSレコードをCloudflareに追加し、Cognitoのカスタムドメイン切り替え（destroy+create、CloudFront経由）をapply。`auth.band-eight.com`でHosted UIが疎通することを確認
- 2026-08-14 backend `main`初回リリースでCI/CD（deploy-prod.yml）が起動し、`environment: production`指定によりOIDCトークンのsubクレームが`ref:refs/heads/main`ではなく`environment:production`形式になり信頼ポリシーと不一致でAssumeRoleWithWebIdentityが失敗する不具合を発見。`environment:`指定を削除し解消（以後の`main` pushで自動デプロイが成功することを確認）
- 2026-08-14 prod ECSタスクの環境変数に`APP_GENERATION_MODE`が未設定で既定のstubのまま稼働しており、Listening音声合成が実際にはPollyを使っていない不具合を発見。`APP_GENERATION_MODE=openai`を追加し解消（S3への実際の音声ファイル生成を確認）
- 2026-08-14 完了: 本番URL（<https://band-eight.com>）でログイン〜Listening問題生成（実Polly音声）〜回答提出〜結果表示、ログアウト（`auth.band-eight.com`経由のグローバルログアウト）までEnd-to-Endで動作確認。なお、回答が正解と完全一致していても不正解判定される採点ロジックの不具合を発見した（本チケットのスコープ外のため、別チケットとして起票予定）
