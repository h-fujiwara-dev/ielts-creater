# #00049 4リポジトリのdevelopからmainへのリリースマージ

- 対象リポジトリ: root / frontend / backend / infra
- ステータス: 未対応
- type: chore
- 関連ドキュメント: [CLAUDE.md ブランチ戦略](../CLAUDE.md#ブランチ戦略) / [#00044 backendのAWSインフラ構築とSupabase接続](./00044_backendのAWSインフラ構築とSupabase接続.md) / [#00045 infraのブランチ戦略を環境ごとに分離](./00045_infraのブランチ戦略を環境ごとに分離.md)

## 実現すること

現在4リポジトリ（root/frontend/backend/infra）すべてで`main`ブランチが初期構築時点（#00003）のまま停止しており、`develop`には複数チケット分の実装（dev環境AWSインフラ構築、Cognito認証、frontend-backend接続等）が未マージのまま蓄積している。本番環境構築（[#00050](./00050_本番環境のAWSインフラ構築とCICDパイプライン整備.md)）・本番デプロイ（[#00051](./00051_frontendとbackendの本番環境への初回デプロイ.md)）に先立ち、各リポジトリの`develop`→`main`リリースPRを作成・マージし、`main`を最新化する。

- 各リポジトリの`release-pr.yml`を`workflow_dispatch`で手動実行してリリースPRを作成する
- infraリポジトリは`develop`（dev環境向け）→`main`のみを対象とする。`prd`（prod環境向け）→`main`のマージは[#00050](./00050_本番環境のAWSインフラ構築とCICDパイプライン整備.md)・[#00051](./00051_frontendとbackendの本番環境への初回デプロイ.md)完了後に別途行う（本チケットの対象外）
- markdownlint等の必須チェックが通過することを確認する
- PRのマージは必ずユーザーが手動で行う

## 受け入れ条件

- [ ] root: `release-pr.yml`を実行し`develop`→`main`のリリースPRを作成し、ユーザーがapprove・マージする
- [ ] frontend: 同様に`develop`→`main`のリリースPRを作成し、ユーザーがapprove・マージする
- [ ] backend: 同様に`develop`→`main`のリリースPRを作成し、ユーザーがapprove・マージする
- [ ] infra: `develop`→`main`のリリースPRを作成し、ユーザーがapprove・マージする（`prd`は対象外）
- [ ] マージ後、各リポジトリの`main`が最新の`develop`内容と一致していることを確認する

## 作業ログ

- 2026-08-13 チケット起票
