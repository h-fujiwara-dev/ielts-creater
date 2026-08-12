# #00051 frontendとbackendの本番環境への初回デプロイ

- 対象リポジトリ: frontend / backend
- ステータス: 未対応
- type: chore
- 関連ドキュメント: [#00043 frontendからdevバックエンドへの接続](./00043_frontendからdevバックエンドへの接続.md) / [#00050 本番環境のAWSインフラ構築とCICDパイプライン整備](./00050_本番環境のAWSインフラ構築とCICDパイプライン整備.md)

## 実現すること

[#00050](./00050_本番環境のAWSインフラ構築とCICDパイプライン整備.md)で整備したprod環境のAWSインフラ・CI/CDパイプラインを使い、frontend（Vercel Production環境）とbackend（prod ECS Fargate）を初めて本番公開する。デプロイ後、実際のユーザー導線（サインアップ〜主要機能、Cognito認証含む）が本番環境で問題なく動作することを確認する。

- 前提として[#00050](./00050_本番環境のAWSインフラ構築とCICDパイプライン整備.md)が完了していること
- [#00043](./00043_frontendからdevバックエンドへの接続.md)（dev版の接続対応）で行った内容の本番版に相当する

## 受け入れ条件

- [ ] frontendをVercelのProduction環境にデプロイし、本番URLでアクセスできることを確認する
- [ ] backendをprod環境のECS Fargateにデプロイし、CI/CDパイプライン経由でのデプロイが成功することを確認する
- [ ] 本番frontendから本番backendへの疎通・認証（Cognito）・主要APIが正常に動作することを確認する
- [ ] 本番環境の動作確認結果を作業ログに記載する

## 作業ログ

- 2026-08-13 チケット起票
