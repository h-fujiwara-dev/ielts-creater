# #00023 履歴一覧画面（S-06）のfrontend実装

- 対象リポジトリ: root / frontend
- ステータス: 完了
- type: feat
- 関連ドキュメント: [画面遷移図](../docs/画面遷移図.md) / [docs/screen-drafts/s-06_history.html](../docs/screen-drafts/s-06_history.html) / [frontendリポジトリ docs/画面設計書/S-06_履歴一覧画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/S-06_履歴一覧画面.md) / [backendリポジトリ docs/API設計書/GET_attempts.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/GET_attempts.md) / [#00019 ダッシュボード画面のfrontend実装](./00019_ダッシュボード画面のfrontend実装.md) / [#00016 画面デザイン叩き台の作成](./00016_画面デザイン叩き台の作成（ベーススタイル・主要フロー6画面）.md)

## 実現すること

`#00016`で作成したHTML叩き台（`docs/screen-drafts/s-06_history.html`）と画面設計書の機能仕様に基づき、履歴一覧画面（S-06）を`ielts-creater-frontend`に実装する。

- 受験履歴一覧、セクション絞り込み、ページング、空状態、各行からの「もう一度解く」（→S-04）「採点結果・解説を再確認」（→S-05）導線をHTML叩き台・画面設計書に基づき実装する（`#00019`で実装する共通アプリナビゲーションシェルを利用する）
- `GET /api/v1/attempts` のレスポンス型をTypeScriptで定義し、モックデータで一覧・絞り込み・ページングを動作させる（実API接続は対象外）

## 受け入れ条件

- [x] 履歴一覧・セクション絞り込み・ページング・空状態・各行の導線（もう一度解く／結果確認）がHTML叩き台の仕様通りに実装されている
- [x] `GET /api/v1/attempts` のレスポンス型定義があり、モックデータで一覧・絞り込み・ページングを確認できる
- [x] `npm run build`が通る
- [x] 既存のlint（ESLint/markdownlint等）が引き続き通る
- [x] ローカルでdev serverを起動し、デスクトップ/モバイルでレイアウト崩れ・コンソールエラーがないことを確認している

## 作業ログ

- 2026-08-10 着手
- 2026-08-10 完了。`ielts-creater-frontend` feat/ticket-00023 で実装（PR #21、#00019〜#00022のブランチに積み上げ）。受験履歴一覧・セクション絞り込み（すべて/Reading/Listening）・ページング・空状態・各行の導線（もう一度解く→S-04、結果を見る→S-05）を実装。`GET /api/v1/attempts`のクエリ/レスポンス型を定義し、静的フィクスチャ（12件）をsection/pageでメモリ内フィルタ・ページングして動作確認。questionSetIdは#00021の実フィクスチャ（mock-qs-reading/listening）を割り当て「もう一度解く」が実際に機能。結果画面（#00022）側のフォールバックが履歴フィクスチャのquestionSetIdを正しく特定できるよう`questionSetIdFromAttemptId`を修正し、「結果を見る」遷移後に正しい設問数が表示されることも確認。claude-in-chromeでデスクトップ(1440px)/モバイル(390px)双方のレイアウト・フィルタ切り替え・ページング・両導線のクリック確認・コンソールエラー0件を確認
