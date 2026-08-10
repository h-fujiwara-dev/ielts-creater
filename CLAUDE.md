# CLAUDE.md

簡易版。後ほどブラッシュアップ予定。

## このリポジトリについて

- IELTS Creator プロジェクトの**ランディング（紹介用）リポジトリ**。アプリコードは含まない
- 業務要件・システム要件・アーキテクチャ・ロードマップを `docs/` に保持
- 実装は [ielts-creater-frontend](https://github.com/h-fujiwara-dev/ielts-creater-frontend) / [ielts-creater-backend](https://github.com/h-fujiwara-dev/ielts-creater-backend) / [ielts-creater-infra](https://github.com/h-fujiwara-dev/ielts-creater-infra) の3リポジトリに分割

## 言語ポリシー

- 日本企業向けポートフォリオとして公開するため、**ドキュメント・コメント・コミットメッセージは日本語**で記載する
- コード上の識別子（変数名・関数名・クラス名・ファイル名等）は英語のまま実装する

## コミットメッセージ規則

- 書式: `[#チケット番号] type: 概要（日本語）`（例: `[#00001] feat: リポジトリ新規構築`）
- チケット番号は5桁ゼロ埋め。typeはConventional Commits準拠（feat/fix/docs/style/refactor/test/chore/perf/build/ci/revert）
- ielts-creater / -frontend / -backend / -infra の4リポジトリ共通のルール。リポジトリ別のプレフィックス（f/b/i等）は付けない
- テンプレートは `.gitmessage`（`git config commit.template .gitmessage` で有効化済み。cloneし直した場合は再設定が必要）
- チケット番号は本リポジトリの[tickets/](./tickets/)で採番・管理する

## ブランチ戦略

- `main`: リリース専用ブランチ。直接pushは禁止（管理者含む、PR必須）。`develop`からのリリースPRをマージするタイミングのみ更新する
- `develop`: 開発統合ブランチ。直接pushは禁止（管理者含む、PR必須）。作業ブランチからのPRはすべてここにマージする
- 作業ブランチ: `develop`から作成し、コミットメッセージのtype（Conventional Commits準拠）を接頭辞とする（例: `feat/xxx`, `fix/xxx`, `docs/xxx`, `chore/xxx`）
- 作業ブランチをpushすると、GitHub Actionsが自動で`develop`宛にPRを作成する（[.github/workflows/auto-pr.yml](./.github/workflows/auto-pr.yml)）
- `develop`→`main`のリリースPRは[.github/workflows/release-pr.yml](./.github/workflows/release-pr.yml)を`workflow_dispatch`で手動実行して作成する（botがPRを作成するため、ユーザー自身がapproveできる）
- 全PR（`develop`・`main`とも）はマージ前にユーザーのapprove（レビュー1件）が必須（`required_approving_review_count: 1`、管理者もバイパス不可）。markdownlint必須チェックも従来通り適用
- ベースブランチへのpush時、オープン中のPRを自動で最新化する（[.github/workflows/auto-update-branch.yml](./.github/workflows/auto-update-branch.yml)）。「out-of-date」表示による手動更新操作は基本不要になる
- PRのマージ（`develop`・`main`とも）は必ずユーザーが手動で行う。Claudeは`gh pr merge`等でPRを勝手にマージしない（PR作成まではOK）
- ielts-creater / -frontend / -backend / -infra の4リポジトリ共通のルール

## チケット運用（Claudeが作業する際のルール）

- チケットは[tickets/](./tickets/)配下に1チケット=1ファイルで管理する。運用ルールは[tickets/README.md](./tickets/README.md)を参照（一覧は保持しないため、`ls tickets/`でファイルを直接確認する）
- 新規タスクはまず`docs/ticket-XXXXX`ブランチ（developから作成）で[`tickets/_template.md`](./tickets/_template.md)から新しい番号のチケットファイルを作成し（ステータスは「未対応」のまま）developへPRを出してマージする
- 実装は、チケットファイルがdevelopにマージされたのを確認してから、あらためてdevelopを起点に実装用ブランチを作成して開始する（チケット定義と実装を同一の未マージブランチにまたがらせない）。着手時のステータス変更は不要（「未対応」のまま）
- 進行中に都度rootリポジトリへ進捗反映PRを出す必要はない。完了時に受け入れ条件のチェック・ステータス更新・作業ログ記載をまとめて1回のPRで反映すれば十分
- 実装コミットは `[#チケット番号] type: 概要` でそのチケットに紐づける（対象リポジトリ側で実施）

## ドキュメント

- [tickets/](./tickets/) — チケット管理
- [docs/業務要件定義書.md](./docs/業務要件定義書.md)
- [docs/システム要件定義書.md](./docs/システム要件定義書.md) — アーキテクチャ（8章）を含む
- [docs/画面遷移図.md](./docs/画面遷移図.md)
- [docs/ロードマップ.md](./docs/ロードマップ.md)
