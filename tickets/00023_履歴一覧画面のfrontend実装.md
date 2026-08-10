# #00023 履歴一覧画面（S-06）のfrontend実装

- 対象リポジトリ: root / frontend
- ステータス: 未着手
- type: feat
- 関連ドキュメント: [画面遷移図](../docs/画面遷移図.md) / [docs/screen-drafts/s-06_history.html](../docs/screen-drafts/s-06_history.html) / [frontendリポジトリ docs/画面設計書/S-06_履歴一覧画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/S-06_履歴一覧画面.md) / [backendリポジトリ docs/API設計書/GET_attempts.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/GET_attempts.md) / [#00019 ダッシュボード画面のfrontend実装](./00019_ダッシュボード画面のfrontend実装.md) / [#00016 画面デザイン叩き台の作成](./00016_画面デザイン叩き台の作成（ベーススタイル・主要フロー6画面）.md)

## 実現すること

`#00016`で作成したHTML叩き台（`docs/screen-drafts/s-06_history.html`）と画面設計書の機能仕様に基づき、履歴一覧画面（S-06）を`ielts-creater-frontend`に実装する。

- 受験履歴一覧、セクション絞り込み、ページング、空状態、各行からの「もう一度解く」（→S-04）「採点結果・解説を再確認」（→S-05）導線をHTML叩き台・画面設計書に基づき実装する（`#00019`で実装する共通アプリナビゲーションシェルを利用する）
- `GET /api/v1/attempts` のレスポンス型をTypeScriptで定義し、モックデータで一覧・絞り込み・ページングを動作させる（実API接続は対象外）

## 受け入れ条件

- [ ] 履歴一覧・セクション絞り込み・ページング・空状態・各行の導線（もう一度解く／結果確認）がHTML叩き台の仕様通りに実装されている
- [ ] `GET /api/v1/attempts` のレスポンス型定義があり、モックデータで一覧・絞り込み・ページングを確認できる
- [ ] `npm run build`が通る
- [ ] 既存のlint（ESLint/markdownlint等）が引き続き通る
- [ ] ローカルでdev serverを起動し、デスクトップ/モバイルでレイアウト崩れ・コンソールエラーがないことを確認している

## 作業ログ

- YYYY-MM-DD 着手
