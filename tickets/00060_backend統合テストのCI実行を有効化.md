# #00060 backend統合テストのCI実行を有効化

- 対象リポジトリ: root / backend
- ステータス: 完了
- type: ci
- 関連ドキュメント: [#00059 単体テストの拡充とバグ修正](./00059_単体テストの拡充とバグ修正.md) / [backendリポジトリ docs/実装規約.md 5章 テスト方針](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/実装規約.md#5-テスト方針) / [backendリポジトリ .github/workflows/backend-ci.yml](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/.github/workflows/backend-ci.yml)

## 実現すること

[#00059](./00059_単体テストの拡充とバグ修正.md)（単体テストの拡充とバグ修正）の作業中、`ielts-creater-backend`の`.github/workflows/backend-ci.yml`が`./gradlew spotlessCheck test`のみを実行しており、`integrationTest`タスク（`@Tag("integration")`、`@SpringBootTest` + Testcontainers、実PostgreSQLコンテナが必要）を一度も実行していないことが判明した。既存9件＋#00059で追加した2件、計11件の`*IntegrationTest`クラスは、CI上ではコンパイル確認のみで**実際に実行されたことが一度もない**状態にある（ローカル開発機にDockerが無い環境でも同様にスキップされてきた可能性が高い）。Controller層（`AttemptController`/`DashboardController`/`MeController`/`QuestionSetController`/`GuestAuthController`）やRepository層の検証はほぼ全てこの統合テスト層に依存しているため、実質的にCIでの検証対象から漏れ続けてきたことになる。本チケットでGitHub Actions上でTestcontainersが動作する状態を確認し、`integrationTest`をCI必須チェックに組み込む。

- GitHub Actions（`ubuntu-latest`）でDockerが利用可能か実機確認する（Testcontainersが要求するDocker socket・権限まわりを含む）。利用できない場合は`docker/setup-docker-action`等の追加設定要否を判断する
- `backend-ci.yml`に`integrationTest`タスクの実行ステップを追加する（既存の`spotlessCheck test`ジョブに追加するか、別ジョブに分離するかは実装時に判断してよい。Testcontainersのコンテナ起動時間を考慮しタイムアウト設定を見直す）
- 実際にCI上で11件の統合テストクラスを実行し、全てグリーンであることを確認する。もし実行してみて落ちるテストがあれば、原因を調査し修正する（環境差異による偽陽性か、実際のバグかを切り分ける）
- `docs/実装規約.md`5.1節の「PRの通常チェック」という記述が実態と合っていなかった点も含め、必要であれば`integrationTest`のCI組み込み後の運用（例: 実行時間が長い場合の扱い）をドキュメントに追記する
- 対象外: Testcontainers以外への統合テスト基盤の変更、既存統合テストのリファクタリング（落ちたものの最小修正を除く）

## 受け入れ条件

- [x] GitHub Actions上でDockerが利用可能であることを確認している（追加設定が必要だった場合はその内容を作業ログに記載する）
- [x] `backend-ci.yml`に`integrationTest`の実行ステップが追加され、PRの必須チェックとして機能している
- [x] 既存9件＋#00059で追加した2件を含む全ての`*IntegrationTest`クラスがCI上で実際に実行され、グリーンであることを確認している
- [x] CI実行時にのみ顕在化した不具合があれば、原因を特定し修正している
- [x] `./gradlew spotlessCheck test integrationTest`がローカル（Docker利用可能環境）でもグリーンであることを確認している

## 作業ログ

- 2026-08-22 起票
- 2026-08-22 着手。`backend-ci.yml`の`spotlessCheck & test`ジョブ内に`integrationTest`実行ステップを追加。GitHub Actions `ubuntu-latest`ランナーはDocker標準搭載のため追加設定は不要だった（`docker/setup-docker-action`等は使わずそのまま動作）。初回CI実行で`QuestionSetApiIntegrationTest`の3件（`readingGenerationReachesReadyAndDetailIsRetrievable`/`listeningGenerationReachesReadyWithDownloadableAudioSegments`/`additionalGenerationRequestIsRateLimitedOnceDailyLimitIsReached`）が429 TOO_MANY_REQUESTSで失敗
  - **原因調査**: ローカルにDocker Desktopを起動し`./gradlew integrationTest`で再現。`attempt-fixture.sql`・`dashboard-fixture.sql`が同一dev-user向けに計3件の`question_set`を`created_at`省略（=本日日付）で挿入しており、`QuestionSetGenerationService.checkDailyLimit`（同一user_idの当日生成2件上限）を、同一JVM内で後続実行される`QuestionSetApiIntegrationTest`が開始する前に使い切っていたことが判明。各`*IntegrationTest`クラスがこれまで単独でしか実行されてこなかった（本チケットが初めてのCI一括実行）ため顕在化していなかった
  - **修正**: 両フィクスチャの`question_set.created_at`を明示的に過去日付（2日前）に固定し、当日カウントの対象外にすることで解消。`./gradlew spotlessCheck test integrationTest`がローカル・CIともにグリーンであることを確認
  - `docs/実装規約.md`1.3節・5章の該当記述も実態（`integrationTest`がCI必須チェックに含まれる）に合わせて更新
  - [backend PR #40](https://github.com/h-fujiwara-dev/ielts-creater-backend/pull/40)を作成・マージ済み
- 2026-08-22 [backend PR #40](https://github.com/h-fujiwara-dev/ielts-creater-backend/pull/40)のマージを確認。受け入れ条件を全てチェックしステータスを「完了」に更新
