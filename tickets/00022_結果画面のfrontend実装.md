# #00022 結果画面（S-05）のfrontend実装

- 対象リポジトリ: root / frontend
- ステータス: 未着手
- type: feat
- 関連ドキュメント: [画面遷移図](../docs/画面遷移図.md) / [docs/screen-drafts/s-05_result.html](../docs/screen-drafts/s-05_result.html) / [frontendリポジトリ docs/画面設計書/S-05_結果画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/S-05_結果画面.md) / [backendリポジトリ docs/API設計書/GET_attempts-id.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/GET_attempts-id.md) / [GET_attempts-id-answers.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/GET_attempts-id-answers.md) / [#00011 ダッシュボード起点への画面遷移フロー変更](./00011_ダッシュボード起点への画面遷移フロー変更.md) / [#00019 ダッシュボード画面のfrontend実装](./00019_ダッシュボード画面のfrontend実装.md) / [#00016 画面デザイン叩き台の作成](./00016_画面デザイン叩き台の作成（ベーススタイル・主要フロー6画面）.md)

## 実現すること

`#00016`で作成したHTML叩き台（`docs/screen-drafts/s-05_result.html`）と画面設計書の機能仕様に基づき、結果画面（S-05）を`ielts-creater-frontend`に実装する。

- スコアサマリー、設問ごとの正誤／解説、再挑戦導線（→S-03）、ダッシュボードへ戻るボタン（`#00011`で追加済みの仕様）をHTML叩き台・画面設計書に基づき実装する（`#00019`で実装する共通アプリナビゲーションシェルを利用する）
- `GET /api/v1/attempts/{id}`・`GET /api/v1/attempts/{id}/answers` のレスポンス型をTypeScriptで定義し、モックデータで表示させる（実API接続は対象外）

## 受け入れ条件

- [ ] スコアサマリー・設問ごとの正誤/解説・再挑戦導線・ダッシュボードへ戻るボタンがHTML叩き台の仕様通りに実装されている
- [ ] `GET /api/v1/attempts/{id}`・`GET /api/v1/attempts/{id}/answers` のレスポンス型定義があり、モックデータで表示確認できる
- [ ] `npm run build`が通る
- [ ] 既存のlint（ESLint/markdownlint等）が引き続き通る
- [ ] ローカルでdev serverを起動し、デスクトップ/モバイルでレイアウト崩れ・コンソールエラーがないことを確認している

## 作業ログ

- YYYY-MM-DD 着手
