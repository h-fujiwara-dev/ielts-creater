# #00073 dev環境ECSヘルスチェックstartPeriod未適用の解消

- 対象リポジトリ: infra
- ステータス: 未対応
- type: fix
- 関連ドキュメント:

## 実現すること

[#00071](./00071_本番SupabaseのRLS未設定エラーを解消.md) のdev環境デプロイ作業中に発見した問題。

`terraform/modules/ecs/main.tf`のECSヘルスチェック`startPeriod`は、prod実機でのSpring Boot起動時間（Flyway検証+JPA初期化含め約113秒）を踏まえ[#00058](./00058_ゲスト機能の本番環境への反映.md)で180秒に修正済み（コミット`2e6e9a2`）だが、dev環境（`envs/dev`）へは`terraform apply`が未実施で、実際のECSタスク定義は旧値の`startPeriod=60`のままだった。

dev環境で`aws ecs update-service --force-new-deployment`を実行したところ、Spring Boot起動（実測108秒）がstartPeriod（60秒）を超え、ヘルスチェック失敗でタスクが繰り返し強制終了される事象が発生した（最終的には偶然起動が早く終わり成功したが、次回以降のデプロイで再発しうる）。

`terraform apply`を試みたが、`terraform/envs/dev/terraform.tfvars`に[#00057](./00057_Cognito認証メールをResend経由に切り替え.md)で追加された`resend_api_key`/`resend_from_email`変数が未設定のため、実行できなかった（tfvarsはgitignore対象・ローカル管理のため、ユーザーの手元での追記が必要）。

## 受け入れ条件

- [ ] `terraform/envs/dev/terraform.tfvars`に`resend_api_key`/`resend_from_email`を追記する
- [ ] dev環境で`terraform apply`を実行し、ECSタスク定義のヘルスチェック`startPeriod`が180秒になっていることを確認する
- [ ] `aws ecs update-service --force-new-deployment`等でタスクを再起動し、ヘルスチェックが安定して通過することを確認する

## 作業ログ

- 2026-09-02 起票
