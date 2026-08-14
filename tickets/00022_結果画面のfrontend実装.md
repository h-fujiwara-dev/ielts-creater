# #00022 結果画面（S-05）のfrontend実装

- 対象リポジトリ: root / frontend
- ステータス: 完了
- type: feat
- 関連ドキュメント: [画面遷移図](../docs/画面遷移図.md) / [docs/screen-drafts/s-05_result.html](../docs/screen-drafts/s-05_result.html) / [frontendリポジトリ docs/画面設計書/S-05_結果画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/S-05_結果画面.md) / [backendリポジトリ docs/API設計書/GET_attempts-id.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/GET_attempts-id.md) / [GET_attempts-id-answers.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/GET_attempts-id-answers.md) / [#00011 ダッシュボード起点への画面遷移フロー変更](./00011_ダッシュボード起点への画面遷移フロー変更.md) / [#00019 ダッシュボード画面のfrontend実装](./00019_ダッシュボード画面のfrontend実装.md) / [#00016 画面デザイン叩き台の作成](./00016_画面デザイン叩き台の作成（ベーススタイル・主要フロー6画面）.md)

## 実現すること

`#00016`で作成したHTML叩き台（`docs/screen-drafts/s-05_result.html`）と画面設計書の機能仕様に基づき、結果画面（S-05）を`ielts-creater-frontend`に実装する。

- スコアサマリー、設問ごとの正誤／解説、再挑戦導線（→S-03）、ダッシュボードへ戻るボタン（`#00011`で追加済みの仕様）をHTML叩き台・画面設計書に基づき実装する（`#00019`で実装する共通アプリナビゲーションシェルを利用する）
- `GET /api/v1/attempts/{id}`・`GET /api/v1/attempts/{id}/answers` のレスポンス型をTypeScriptで定義し、モックデータで表示させる（実API接続は対象外）

## 受け入れ条件

- [x] スコアサマリー・設問ごとの正誤/解説・再挑戦導線・ダッシュボードへ戻るボタンがHTML叩き台の仕様通りに実装されている
- [x] `GET /api/v1/attempts/{id}` のレスポンス型定義があり、モックデータで表示確認できる（画面設計書のAPI/データ連携表を精査した結果、本画面が実際に利用するのは`POST .../submit`と同一形状の`GET /attempts/{id}`のみで、`GET /attempts/{id}/answers`は回答画面(S-04)の下書き復元専用のため対象外と判断）
- [x] `npm run build`が通る
- [x] 既存のlint（ESLint/markdownlint等）が引き続き通る
- [ ] ローカルでdev serverを起動し、デスクトップ/モバイルでレイアウト崩れ・コンソールエラーがないことを確認している（デスクトップ1440pxは確認済み。モバイル幅はブラウザ自動化ツールのウィンドウリサイズがこの環境で不安定だったため未確認。既存の同一レスポンシブパターンから問題ない可能性が高いが、要目視確認）

## 作業ログ

- 2026-08-10 着手
- 2026-08-10 完了。`ielts-creater-frontend` feat/ticket-00022 で実装（PR #19、#00019〜#00021のブランチに積み上げ）。スコアサマリー（ドーナツ型SVG・正答数/総設問数・条件バッジ・提出日時/所要時間・再挑戦/ダッシュボードへ戻るボタン）、設問ごとの結果一覧（正解/不正解/未回答の3値バッジ、不正解時のみ正解・解説を表示）を実装。ルーティングは画面設計書が提示する2案のうち`/attempts/{attemptId}/result`を採用。`GET /api/v1/attempts/{id}`はPOST submitと同一形状のためAttemptResult型を共有。提出日時・所要時間・設問文はAttemptResult型に存在しないためモック専用の補助データ/関数として別管理。claude-in-chromeで実際に提出→結果表示、再挑戦ボタンのプリフィル（S-03への遷移）、sessionStorage未保存時のフォールバック表示までデスクトップ(1440px)で確認しコンソールエラー0件。モバイル幅の確認はブラウザ自動化ツールのウィンドウリサイズがこの環境で不安定だったため実施できていないが、#00019〜#00021で繰り返し検証済みの同一レスポンシブパターン（`max-w-3xl`・`flex-wrap`・`sm:`ブレークポイント）を使用
