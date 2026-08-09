# #00002 GitHub運用ルールとコミット規約の自動化

- 対象リポジトリ: root
- ステータス: 進行中
- type: chore
- 関連ドキュメント: [CLAUDE.md](../CLAUDE.md#コミットメッセージ規則)

## 実現すること

mainブランチの保護（PR必須・管理者含む・レビュー0件でも可）、push時のPR自動作成、コミット時のmarkdownlint、コミットメッセージ規約のcommitlint強制、PR時のCI lint、Secret Scanning・Push Protectionの確認、マージ後ブランチ自動削除・Squashマージ限定を整備し、開発フローのガバナンスを自動化する。

## 受け入れ条件

- [ ] mainへの直接pushが拒否される（PR必須、管理者含む）
- [ ] featureブランチpush時にPRが自動作成される
- [ ] 規約外のコミットメッセージがcommit-msgフックで拒否される
- [ ] markdownlint違反がpre-commitで拒否される
- [ ] PR上でmarkdownlintのCIチェックが必須ステータスとして実行される
- [ ] マージ後にheadブランチが自動削除され、squashマージのみ選択可能

## 作業ログ

- 2026-08-09 着手
- 2026-08-09 Phase A（Husky/lint-staged/commitlint/markdownlint）・Phase B（GitHub Actions: 自動PR作成・PR Lint）・Phase C前半（Actions権限・Squash限定/ブランチ自動削除・Secret Scanning確認）完了。ブランチ保護有効化前の実地動作確認を実施中
