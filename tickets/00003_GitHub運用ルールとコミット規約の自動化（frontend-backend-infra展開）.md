# #00003 GitHub運用ルールとコミット規約の自動化（frontend/backend/infra展開）

- 対象リポジトリ: frontend / backend / infra
- ステータス: 進行中
- type: chore
- 関連ドキュメント: [CLAUDE.md](../CLAUDE.md#コミットメッセージ規則) / [#00002](./00002_GitHub運用ルールとコミット規約の自動化.md)

## 実現すること

root（本リポジトリ）で整備済みの開発ガバナンス（[#00002](./00002_GitHub運用ルールとコミット規約の自動化.md)）を、姉妹リポジトリ `ielts-creater-frontend` / `ielts-creater-backend` / `ielts-creater-infra` にも同様に展開する。3リポジトリとも現時点では実コードがなく、markdownlintのみを対象とする。ESLint/Checkstyle/tflint等の言語別Lintは実コード追加後に別途対応する。

- 各リポジトリのmainブランチ保護（PR必須・管理者含む・レビュー0件でも可）
- push時のPR自動作成（GitHub Actions）
- コミット時のmarkdownlint（Husky pre-commit + lint-staged）
- コミットメッセージ規約のcommitlint強制（Husky commit-msg）
- PR時のCI lint（GitHub Actions、必須ステータスチェック）
- マージ後ブランチ自動削除・Squashマージのみへの制限

## 受け入れ条件

- [ ] frontend: mainへの直接pushが拒否される（PR必須、管理者含む）
- [ ] frontend: featureブランチpush時にPRが自動作成され、markdownlintの必須CIチェックが実行される
- [ ] frontend: 規約外のコミットメッセージ・markdownlint違反がローカルフックで拒否される
- [ ] backend: mainへの直接pushが拒否される（PR必須、管理者含む）
- [ ] backend: featureブランチpush時にPRが自動作成され、markdownlintの必須CIチェックが実行される
- [ ] backend: 規約外のコミットメッセージ・markdownlint違反がローカルフックで拒否される
- [ ] infra: mainへの直接pushが拒否される（PR必須、管理者含む）
- [ ] infra: featureブランチpush時にPRが自動作成され、markdownlintの必須CIチェックが実行される
- [ ] infra: 規約外のコミットメッセージ・markdownlint違反がローカルフックで拒否される

## 作業ログ

- 2026-08-09 着手
