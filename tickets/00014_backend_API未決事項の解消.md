# #00014 backend API未決事項の解消

- 対象リポジトリ: root / backend / frontend
- ステータス: 完了
- type: docs
- 関連ドキュメント:
  - [システム要件定義書 8章 アーキテクチャ](../docs/システム要件定義書.md#8-アーキテクチャ)
  - [tickets/00007_フロントエンド画面設計書の作成.md](./00007_フロントエンド画面設計書の作成.md)
  - [backendリポジトリ docs/API一覧.md](https://github.com/h-fujiwara-dev/ielts-creater-backend/blob/main/docs/API一覧.md)

## 実現すること

backendリポジトリに既に作り込まれているAPI設計（`docs/API一覧.md`・`docs/API設計書/`・`docs/ER図・テーブル定義.md`）を土台に、`#00007`のフロントエンド画面設計書作成時に検出された6件の未決事項を解消し、Phase1 MVPで必要なAPI設計を完成させる。

- 未提出の保存済み回答を取得するAPI（`GET /api/v1/attempts/{id}/answers`）の新設
- `GET /api/v1/attempts`レスポンスへの`questionSetId`追加
- `GET /api/v1/attempts`の絞り込み・ページングクエリパラメータ（`section`/`page`/`size`）の明文化、対象を`SUBMITTED`のみに限定
- `GET /api/v1/dashboard/summary`への期間・セクション絞り込みクエリパラメータ（`period`/`section`、サーバー側フィルタ）の追加
- `POST /api/v1/question-sets`のトピック未入力時の挙動確定（任意化・サーバー側ランダム選択・文字数上限100文字）
- `GET /api/v1/question-sets/{id}`への`topic`/`difficulty`フィールド追加（再挑戦時の条件プリフィル対応）

削除系（DELETE）APIは要件定義書に記載がなく、個人開発ポートフォリオのスコープ外として今回は対象外とする。

## 受け入れ条件

- [x] `backend/docs/API設計書/GET_attempts-id-answers.md`を新規作成し、`API一覧.md`に反映する
- [x] `backend/docs/API設計書/GET_attempts.md`に`questionSetId`・クエリパラメータ・対象ステータスを明記する
- [x] `backend/docs/API設計書/GET_dashboard-summary.md`に`period`/`section`クエリパラメータを明記する
- [x] `backend/docs/API設計書/POST_question-sets.md`にトピック未入力時の挙動・文字数上限を明記する
- [x] `backend/docs/API設計書/GET_question-sets-id.md`に`topic`/`difficulty`フィールドを追加する
- [x] `frontend/docs/画面設計書/S-03,S-04,S-05,S-06,S-07`の実装メモから解消済みの未決事項を更新する

## 作業ログ

- 2026-08-10 着手。ielts-createrリポジトリの業務要件定義書・システム要件定義書・画面遷移図・`#00007`/`#00011`チケット、およびbackend/frontend両リポジトリの既存設計書を調査し、未決事項6件を特定
- 2026-08-10 backendリポジトリの該当API設計書5件を更新・1件新規作成、frontendリポジトリの画面設計書5件（S-03/S-04/S-05/S-06/S-07）の実装メモを更新し、完了
