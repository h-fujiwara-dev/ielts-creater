# #00021 回答画面（S-04）のfrontend実装

- 対象リポジトリ: root / frontend
- ステータス: 完了
- type: feat
- 関連ドキュメント: [画面遷移図](../docs/画面遷移図.md) / [docs/screen-drafts/s-04_answer.html](../docs/screen-drafts/s-04_answer.html) / [frontendリポジトリ docs/画面設計書/S-04_回答画面.md](https://github.com/h-fujiwara-dev/ielts-creater-frontend/blob/main/docs/画面設計書/S-04_回答画面.md) / [backendリポジトリ docs/API設計書/GET_question-sets-id.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/GET_question-sets-id.md) / [GET_question-sets-id-audio-segments.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/GET_question-sets-id-audio-segments.md) / [POST_attempts.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/POST_attempts.md) / [PATCH_attempts-id-answers.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/PATCH_attempts-id-answers.md) / [POST_attempts-id-submit.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API設計書/POST_attempts-id-submit.md) / [#00019 ダッシュボード画面のfrontend実装](./00019_ダッシュボード画面のfrontend実装.md) / [#00016 画面デザイン叩き台の作成](./00016_画面デザイン叩き台の作成（ベーススタイル・主要フロー6画面）.md)

## 実現すること

`#00016`で作成したHTML叩き台（`docs/screen-drafts/s-04_answer.html`）と画面設計書の機能仕様に基づき、回答画面（S-04）を`ielts-creater-frontend`に実装する。

- Reading（本文＋TFNG／穴埋め／見出しマッチング）・Listening（音声プレイヤー＋MCQ）の2パターンをHTML叩き台・画面設計書に基づき実装する（`#00019`で実装する共通アプリナビゲーションシェルを利用する）
- 問題取得・音声セグメント取得・受験開始・回答一時保存（`#00014`で解消済みの回答復元用APIを含む）・提出に関わる各APIのリクエスト/レスポンス型をTypeScriptで定義し、モックデータで動作させる（実API接続は対象外）

## 受け入れ条件

- [x] Reading・Listeningの2パターンがHTML叩き台の仕様通りに実装されている（TFNG/穴埋め/見出しマッチング/MCQ含む）
- [x] 関連APIのリクエスト/レスポンス型定義があり、モックデータで回答操作・提出遷移を確認できる
- [x] `npm run build`が通る
- [x] 既存のlint（ESLint/markdownlint等）が引き続き通る
- [x] ローカルでdev serverを起動し、デスクトップ/モバイルでレイアウト崩れ・コンソールエラーがないことを確認している

## 作業ログ

- 2026-08-10 着手
- 2026-08-10 完了。`ielts-creater-frontend` feat/ticket-00021 で実装（PR #18、#00019/#00020のブランチに積み上げ）。Reading（本文+TFNG3問/穴埋め2問/見出しマッチング2問）・Listening（音声プレイヤー+MCQ2問）の2パターンを実装。問題取得・音声セグメント取得・受験開始・回答自動保存（デバウンス）・回答復元・提出の各APIの型を定義し、モックの採点ロジック（正答データは非公開の内部フィクスチャ）とsessionStorageによる下書き/結果の永続化で動作確認。claude-in-chromeで実際に回答入力→リロード復元→提出→結果画面遷移（`/attempts/{attemptId}/result`、#00022で実装予定）までの一連の流れとデスクトップ(1440px)/モバイル(390px)のレイアウト・コンソールエラー0件を確認
