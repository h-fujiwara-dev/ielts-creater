# #00061 不要ファイル削除とscreen-draftsのアーカイブ化

- 対象リポジトリ: root
- ステータス: 完了
- type: chore
- 関連ドキュメント: [#00016 画面デザイン叩き台の作成](./00016_画面デザイン叩き台の作成（ベーススタイル・主要フロー6画面）.md)

## 実現すること

- Playwright MCPツールが生成した一時ファイル（`.playwright-mcp/`）を削除し、`.gitignore`に追記して再発を防ぐ
- 役目を終えた画面デザイン叩き台一式（`docs/screen-drafts/`）を`docs/archive/screen-drafts/`へ移動する。移動元パスを参照している完了済みチケット（#00016, #00018〜#00024）は当時の記録として書き換えず、移動先READMEに移設の経緯を注記する

## 受け入れ条件

- [x] `.playwright-mcp/`が削除されている
- [x] `.gitignore`に`.playwright-mcp/`が追記されている
- [x] `docs/screen-drafts/`が`docs/archive/screen-drafts/`へ移動されている
- [x] 移動先`docs/archive/screen-drafts/README.md`内の自己参照パス（`open`コマンド例、tickets/00016への相対リンク）が新しい階層に追随して修正されている
- [x] 移動先READMEに「移設日・旧パス・完了済みチケットのリンクは更新していない旨」が注記されている

## 作業ログ

- 2026-08-22 着手
- 2026-08-23 `.playwright-mcp/`を削除し`.gitignore`に追記。`docs/screen-drafts/`を`docs/archive/screen-drafts/`へ`git mv`し、移動先README.mdの自己参照パス（openコマンド例、tickets/00016への相対リンク）を修正の上、移設の経緯を注記。受け入れ条件を全て満たしチケットを完了とする
