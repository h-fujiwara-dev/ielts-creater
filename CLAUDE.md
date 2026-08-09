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

## チケット運用（Claudeが作業する際のルール）

- チケットは[tickets/](./tickets/)配下に1チケット=1ファイルで管理する。運用ルール・一覧は[tickets/README.md](./tickets/README.md)を参照
- 新規タスクに着手する前に、[`tickets/_template.md`](./tickets/_template.md)から新しい番号でチケットファイルを作成し、ステータスを「進行中」にする
- 作業中は該当チケットの作業ログに進捗を追記する
- 完了したら受け入れ条件にチェックを入れ、ステータスを「完了」にし、[tickets/README.md](./tickets/README.md)の一覧も更新する
- 実装コミットは `[#チケット番号] type: 概要` でそのチケットに紐づける（対象リポジトリ側で実施）

## ドキュメント

- [tickets/](./tickets/) — チケット管理
- [docs/業務要件定義書.md](./docs/業務要件定義書.md)
- [docs/システム要件定義書.md](./docs/システム要件定義書.md) — アーキテクチャ（8章）を含む
- [docs/画面遷移図.md](./docs/画面遷移図.md)
- [docs/ロードマップ.md](./docs/ロードマップ.md)
