# #00019 ダッシュボード画面（S-07）のfrontend実装

- 対象リポジトリ: root / frontend
- ステータス: 完了
- type: feat
- 関連ドキュメント: [画面遷移図](../docs/画面遷移図.md) / [docs/screen-drafts/s-07_dashboard.html](../docs/screen-drafts/s-07_dashboard.html) / [docs/screen-drafts/00_style-guide.html](../docs/screen-drafts/00_style-guide.html)（アプリナビゲーションシェル参照）/ [frontendリポジトリ docs/画面設計書/S-07_ダッシュボード画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/S-07_ダッシュボード画面.md) / [backendリポジトリ docs/API設計書/GET_dashboard-summary.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/GET_dashboard-summary.md) / [GET_me.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/GET_me.md) / [#00016 画面デザイン叩き台の作成](./00016_画面デザイン叩き台の作成（ベーススタイル・主要フロー6画面）.md)

## 実現すること

`#00016`で作成したHTML叩き台（`docs/screen-drafts/s-07_dashboard.html`）と画面設計書の機能仕様に基づき、ダッシュボード画面（S-07）を`ielts-creater-frontend`に実装する。ログイン後の起点（ハブ）画面であるため、本チケットでログイン後全画面の土台となる共通アプリナビゲーションシェルもあわせて実装する。

- ログイン後の全画面（S-03〜S-07）で共通利用する**アプリナビゲーションシェル**（ロゴ＋ナビリンク「ダッシュボード／問題生成／履歴一覧」＋ユーザーメニュー）を、共通レイアウト/コンポーネントとして実装する。以降の`#00020`〜`#00023`はこれを再利用する前提とする（TOPのマーケティング用ヘッダーとは別物）
- ダッシュボード画面（スコア推移チャート、セクション別／出題形式別正答率、受験回数）をHTML叩き台・画面設計書に基づき実装する
- `GET /api/v1/dashboard/summary`・`GET /api/v1/me` のレスポンス型をTypeScriptで定義し、モックデータで画面を動作させる（実API接続は対象外）

## 受け入れ条件

- [x] 共通アプリナビゲーションシェルが独立したレイアウト/コンポーネントとして実装され、後続画面から再利用可能な設計になっている
- [x] ダッシュボード画面がHTML叩き台・画面設計書の仕様通りに実装されている（スコア推移・セクション別/出題形式別正答率・受験回数）
- [x] `GET /api/v1/dashboard/summary`・`GET /api/v1/me` のレスポンス型定義があり、モックデータで表示確認できる
- [x] `npm run build`が通る
- [x] 既存のlint（ESLint/markdownlint等）が引き続き通る
- [x] ローカルでdev serverを起動し、デスクトップ/モバイルでレイアウト崩れ・コンソールエラーがないことを確認している

## 作業ログ

- 2026-08-10 着手
- 2026-08-10 frontend [PR #15](https://github.com/h-fujiwara-dev/ielts-creater-frontend/pull/15)で共通アプリナビゲーションシェルとダッシュボード画面（S-07）を実装しdevelopへマージ済み。ステータス更新が漏れていたため今回反映
