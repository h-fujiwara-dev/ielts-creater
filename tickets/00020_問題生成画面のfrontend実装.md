# #00020 問題生成画面（S-03）のfrontend実装

- 対象リポジトリ: root / frontend
- ステータス: 未着手
- type: feat
- 関連ドキュメント: [画面遷移図](../docs/画面遷移図.md) / [docs/screen-drafts/s-03_question-generation.html](../docs/screen-drafts/s-03_question-generation.html) / [frontendリポジトリ docs/画面設計書/S-03_問題生成画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/S-03_問題生成画面.md) / [backendリポジトリ docs/API設計書/POST_question-sets.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/POST_question-sets.md) / [#00019 ダッシュボード画面のfrontend実装](./00019_ダッシュボード画面のfrontend実装.md) / [#00016 画面デザイン叩き台の作成](./00016_画面デザイン叩き台の作成（ベーススタイル・主要フロー6画面）.md)

## 実現すること

`#00016`で作成したHTML叩き台（`docs/screen-drafts/s-03_question-generation.html`）と画面設計書の機能仕様に基づき、問題生成画面（S-03）を`ielts-creater-frontend`に実装する。

- 生成条件入力／生成中／生成失敗の3状態をHTML叩き台・画面設計書に基づき実装する（`#00019`で実装する共通アプリナビゲーションシェルを利用する）
- `POST /api/v1/question-sets` のリクエスト/レスポンス型をTypeScriptで定義し、モック送信でS-04（回答画面）への画面遷移を確認する（実API接続は対象外）

## 受け入れ条件

- [ ] 生成条件入力／生成中／生成失敗の3状態がHTML叩き台の仕様通りに実装されている
- [ ] `POST /api/v1/question-sets` のリクエスト/レスポンス型定義があり、モックでS-04への遷移を確認できる
- [ ] `npm run build`が通る
- [ ] 既存のlint（ESLint/markdownlint等）が引き続き通る
- [ ] ローカルでdev serverを起動し、デスクトップ/モバイルでレイアウト崩れ・コンソールエラーがないことを確認している

## 作業ログ

- YYYY-MM-DD 着手
